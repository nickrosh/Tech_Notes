
*Domain Name System* (DNS) is a hierarchical and distributed naming system for computers, services, and other resources in the [internet](TCP-IP%20Model.md). 

![](../Attachments/Pasted%20image%2020230124235955.png)

With DNS, we can use a *domain* rather than an [IP](IP.md) address to communicate with another resource or server. This contains the protocol (almost always [HTTP](HTTP.md) or HTTPS) then the subdomain (almost always www or World-Wide-Web) and then the actual domain name of the website. This is then followed by a *Top Level Domain* which is a fixed suffix to the domain (e.g. `.com`, `.org`, `.net`).

![](../Attachments/Pasted%20image%2020230124235013.png)

Let's say you want to navigate to `www.example.com`. There are authoritative root naming servers (including one at UMD) that contain the agreed upon mapping of IP addresses to domains, we start with those to get the actual address of the website we want: 

1. you first go to the DNS root name server. It will then tell you the address for the name server for the `.com` top level domain. 
2. Then you go to the `.com` name server. This name server then sends you the address for the name server of the website you want to visit, `www.example.com`
3. Finally, you visit the name server for `www.example.com`, and it returns the [IP](IP.md) address of the actual website. Now you can send a [GET request](HTTP.md) to the IP address of the site to visit it.

## DNS Records

DNS records are the actual instructions that live in the DNS servers and provide information about a domain including what IP address is associated with what domain. All DNS records also  have a "TTL" time-to-live, and indicates how often a DNS server will refresh that record. Here are the most common types of records:
- **A Record**: The record that holds the IPv4 address of a domain (`google.com -> 132.290.340.001`)
- **AAAA Record**: The record that holds the IPv6 address of a domain (`google.com -> 6cfa.7002...`)
- **CNAME Record**: Forwards one domain or subdomain to another domain (`google.com -> www.google.com`)
- **MX Record**: directs mail to an email server
- **NS Record**: stores the name server for a DNS entry