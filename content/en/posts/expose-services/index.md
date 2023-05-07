---
title: "Expose your services to the Internet from your home network"
date: 2023-05-06T00:00:00+02:00
draft: false
showTableOfContents: true
tags: ['homelab', 'cloudflare', 'kubernetes', 'metallb']
description: "You don't want to expose your home network to the Internet but you want to host applications on Kubernetes ? Read this article"
showTaxonomies: true
---
The ultimate goal for a Cloud Engineer and a Sysadmin who is interested in DevOps is to setup a Kubernetes cluster on their servers to manage applications cycles.

On bare-metal infrastructure, this can be quite a challenge, as we do not have any public cloud service to help us.

Using Load Balancers inside of our Kubernetes cluster seems impossible without a proper infrastructure. We do not have any BGP router, nor do we have a physical Load Balancer to guarantee failover. 

In this article, I will be trying to give you a correct solution to expose your Kubernetes cluster to the Internet safely and for free.

## The problem

Every person that has tried to setup a Kubernetes cluster on a bare-metal infrastructure has encountered the `\<pending\>` status under the External-IP field on their LoadBalancer services... and the headaches that followed.

{{< figure
    src="/posts/expose-services/svc-pending.png"
    alt="External-IP \<spending\> on a LoadBalancer service"
    caption="The External-IP field is stuck under \<pending\> status for the LoadBalancer services"
    >}}

Actually, on Public Clouds, the Load Balancing products (Cloud Load Balancing on Google Cloud) assign IP addresses to your Kubernetes services. Without one, Kubernetes doesn't actually know which IP address to assign to a specific Service. 

## The solution

Fortunately, the Kubernetes ecosystem is quite vast and a solution to this specific problem has been developed, it is called [MetalLB](https://metallb.universe.tf). 

{{< alert >}}
Note that the L2 mode of MetalLB doesn't guarantee a zero downtime failover, because of the ARP protocol. While the ARP cache is not refreshed, the users would hit a specific node that is no longer up. It can sometimes last many minutes.
{{</ alert >}}

To install MetalLB, please refer to the documentation here : [MetalLB, bare metal load-balancer for Kubernetes](https://metallb.universe.tf/installation/)

MetalLB (L2 mode) uses [Address Resolution Protocol](https://en.wikipedia.org/wiki/Address_Resolution_Protocol) (ARP) to associate a [Service](https://kubernetes.io/docs/concepts/services-networking/service/) to a specific IP address. This allows Kubernetes to balance the load using the internal `kube-proxy` component that establishes a connection to a specific Pod.  

En pratique, imaginons que l'on dise à MetalLB d'utiliser la plage d'adresses 10.0.0.100-10.0.0.200 pour exposer les services, et que le service `nginx` se voit attribué l'adresse `10.0.0.100` : 

In practice, we tell MetalLB to use a specific pool of IP addresses, 10.0.0.100-10.0.0.200, to expose services. The `nginx` service would have its External IP set to `10.0.0.100`.

*This diagram might not be easily readable if you are using the Dark theme. You can switch to Light theme at the bottom of this page. Sorry for the inconvenience, I am currently trying to fix it.*

{{< mermaid >}}
sequenceDiagram
    participant User
    participant MetalLB Operator
    participant nginx service

    User->>MetalLB Operator: Who is at 10.0.0.100 ?
    MetalLB Operator->>User: 10.0.0.100 is at f9:a7:bc:fe:00:3a
    User->>nginx service: HTTP GET / Request
    nginx service->>User: HTTP Response
{{</ mermaid >}}

{{< badge >}}
Precision
{{</ badge>}}

An ARP request is broadcast (given that we don't know who is at the specific IP address), but the previous diagram has been simplified. The concerned MetalLB Operator (not all MetalLB operators) responds to the ARP request by giving the MAC address of the Service and the Pod of the application. The user can now access the application. Although, the user and the Kubernetes cluster must be located in the same broadcast domain in order for it to work.

Read more about MetalLB L2 (and its limitations) [here](https://metallb.universe.tf/concepts/layer2/).

To indicate an IP Address pool to MetalLB, we can use the following manifest :

{{< highlight yaml "linenos=table" >}}
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: home-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.100-192.168.2.200
{{</ highlight >}}

We must also tell MetalLB to advertise this pool using Layer2 (and not BGP) :

{{< highlight yaml "linenos=table" >}}
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: home
  namespace: metallb-system
spec:
  ipAddressPools:
  - home-pool
{{</ highlight >}}

{{< alert >}}
The given IP addresses must not be in use. It would lead to a conflict, and thus, connectivity issues. To avoid any problem, I recommend you to exclude this pool from your DHCP range.
{{</ alert >}}

Now, MetalLB will assign an IP address to your service, which you can get with `kubectl get svc` :

{{< figure
    src="/posts/expose-services/assigned-address.png"
    alt="MetalLB has assigned the IP address 10.0.0.104 to the blog service"
    caption="MetalLB has assigned the IP address `10.0.0.104` to the `blog` service."
    >}}

Your service is then exposed on your local network. You can access it directly, but you might want to expose it on your domaine name.

You can also install an [Ingress Controller](https://kubernetes.io/fr/docs/concepts/services-networking/ingress/) to route your different domains and subdomains !

## Expose your service to the Internet

Historically, a simple solution was to setup a PAT, which means to bind a port of your public IP address to a specific port of a private IP address. 

This solution has different limitations :
- You would be exposing your home network
- Your public IP address might change (depending on your ISP) 
- You would not always be able to bind a port to a virtual IP address

A possible solution to avoid these limitations would be [Cloudflare Tunnels](https://www.cloudflare.com/products/tunnel/). It is an agent that you can install on a machine of your home network, which will establish a connection between your network and Cloudflare, like an IPSec VPN for instance.

This method has many advantages :
- Cloudflare would be acting as a reverse proxy, thus, would not expose your home network
- Cloudflare would be managing your public TLS certificates (no need to renew them every 3 months !)
- No configuration is required, the agent install being quite easy

{{< alert >}}
This service is free for 50 users maximum, but you would need to indicate a Debit Card or Credit Card, or link your PayPal account. I advise you to use a virtual Debit Card to avoid divulgating your banking informations. 
{{</ alert >}}

To set this service up, you would need to configure the NS fields of your domain name to the Cloudflare servers. Follow the steps on [Cloudflare Dash](https://dash.cloudflare.com). You can then go to the "Zero Trust" category and choose a plan (the free one preferably). Then go to the `Tunnels` page under the `Access` category.

CLick on the `Create a tunnel` button, give a name to your tunnel (for example: home), and choose an install method for the agent. I recommend using Docker and to host it outside of your Kubernetes cluster, so that, if your cluster goes down, the tunnel remains active. Please note that if the agent is down, so is your website.

When the tunnel status becomes `HEALTHY`, you can associate a subdomain (or your main domain) to a private IP address (and even to a specific port if necessary). Cloudflare will host a proxy to your local address via a subdomain, then configure a CNAME field to expose it to your (sub)domain.

{{< figure
    src="/posts/expose-services/tunnel.png"
    alt="Cloudflare Tunnel setup"
    caption="Cloudflare Tunnel setup. Here, `blog.nkirchho.dev` will point to the private IP address `10.0.0.104` provided by MetalLB and designating the `blog` service."
    >}}

If you use an Ingress Controller, you would need to set the value of the `Host` HTTP header manually, under the `HTTP Settings` category. Your controller will then know which service you try to access.

## Alternative solution

Normally, I would have talked about a free and open-source solution to resolve this specific problem. However, given the current Internet state and the difficulties to get IPv4 addresses, the easiest way of solving it was to rely on a 3rd-party provider. Here, Cloudflare. I do not have any specific relation with them, other than being  a customer.

If you do not want to rely on a "Giant tech company", you could use the dynamic DNS principle to assign, even if it changes, your public IP address to a DNS `A` field, using PAT. This does not cancel the other limitations.

Note that you would always rely on a provider for your domain names. This is only a question of opinions. Cloudflare being an omnipresent company on the Internet, and defending freedom of expression, I feel okay using their services.

But that's your choice !

Thank you for reading, please feel free to make any comment to expose-services[at]nkirchho[dot]dev or open a PR on my [blog GitHub](https://github.com/nkirchhoffer/nkirchho.dev). 