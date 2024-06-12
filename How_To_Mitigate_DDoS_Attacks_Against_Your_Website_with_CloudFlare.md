# How To Mitigate DDoS Attacks Against Your Website with CloudFlare | DigitalOcean
### [Introduction](#introduction)

[Cloudflare](https://www.cloudflare.com/) is a company that provides content delivery network (CDN) and distributed DNS services by acting as a reverse proxy for websites. Cloudflare’s free and paid services can be used to improve the security, speed, and availability of a website in a variety of ways.

In this tutorial, you will learn how to use Cloudflare’s free tier service to protect your web servers against ongoing HTTP-based distributed denial of service (DDoS) attacks by enabling **“I’m Under Attack Mode”**. This security mode can mitigate DDoS attacks by presenting an interstitial page to verify the legitimacy of a connection before passing it to your web server.

[Prerequisites](#prerequisites)
-------------------------------

This tutorial assumes that you have the following:

*   A web server
*   A registered domain that points to your web server
*   Access to the control panel of the domain registrar that issued the domain

You must also sign up for a Cloudflare account before continuing.

**Note:** This tutorial will require the use of Cloudflare’s name servers.

[Step 1 - Configuring Your Domain to Use Cloudflare](#step-1-configuring-your-domain-to-use-cloudflare)
-------------------------------------------------------------------------------------------------------

Before using any of Cloudflare’s features, you must configure your domain to use Cloudflare’s DNS.

If you haven’t already done so, log in to Cloudflare.

### [Add a Website and Scan DNS Records](#add-a-website-and-scan-dns-records)

After logging in, you will be taken to the **Get Started with Cloudflare** page. Here, you must click the **Add site** button at the top to add your website to Cloudflare:

![Add a site button](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_add_site_button.png)

Enter the domain name that you want to use Cloudflare with and click the **Add Site** button. You should be taken to a page that looks like this:

![Select Cloudflare Plan](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_free_site.png)

In this tutorial, we will select the **Free plan** option. If you want to pay for a different plan because you want additional Cloudflare features, feel free to do so. Then, click the **Continue** button.

The next page shows the results of the DNS record scan for your site. Be sure that all of your existing DNS records are present, as these are the records that Cloudflare will use to resolve requests to your domain. In our example, we used `flippeddev.com` as the domain:

![Add DNS Records](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_dns_results.png)

Note that, for your A and CNAME records that point to your web server(s), the **Status** column should have an orange cloud with an arrow going through it. This indicates that the traffic will flow through Cloudflare’s reverse proxy before hitting your server(s).

### [Change Your Nameservers](#change-your-nameservers)

The next page will display name servers that need to be removed and the Cloudflare name servers that should be added. Here is an example of what the page might look like:

![Change your nameservers](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_change_nameserver.png)

To change your domain’s nameservers, log in to your domain registrar control panel and make the DNS changes that Cloudflare presented. For example, if you purchased your domain through a registrar like Google or NameCheap, you will need to log into the appropriate registrar’s control panel and make the changes there.

The process varies based on your particular domain registrar. If you can’t figure out how to do this, it is similar to the process described in [How to Point to DigitalOcean Nameservers From Common Domain Registrars](https://www.digitalocean.com/community/tutorials/how-to-point-to-digitalocean-nameservers-from-common-domain-registrars) except you will use the Cloudflare nameservers instead of DigitalOcean’s.

In the example case, the domain is using DigitalOcean’s nameservers and we need to update it to use Cloudflare’s DNS.

When you are finished changing your nameservers, click the **Continue** button. It can take up to 24 hours for the nameservers to switch but it usually only takes several minutes.

### [Wait for Nameservers to Update](#wait-for-nameservers-to-update)

Because updating nameservers takes an unpredictable amount of time, it is likely that you will see this page next:

![Pending nameservers](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_pending_nameserver.png)

The **Pending** status means that Cloudflare is waiting for the nameservers to update to the ones that it prescribed (e.g. `olga.ns.Cloudflare.com` and `rob.ns.Cloudflare.com`). If you changed your domain’s nameservers, all you have to do is wait and check back later for an **Active** status. If you click the **Recheck Nameservers** button or navigate to the Cloudflare dashboard, it will check if the nameservers have updated.

### [Cloudflare Is Active](#cloudflare-is-active)

Once the nameservers update, your domain will be using Cloudflare’s DNS and you will see it has an **Active** status.

This means that Cloudflare is acting as a reverse proxy to your website, and you have access to whichever features are available to the pricing tier that you signed up for. If you’re using the **free** tier, as we are in this tutorial, you will have access to some of the features that can improve your site’s security, speed, and availability.

We won’t cover all of the features in this tutorial, as we are focusing on mitigating ongoing DDoS attacks, but they include CDN, SSL, static content caching, a firewall (before the traffic reaches your server), and traffic analytics tools.

Also note the **Settings Summary**, right below your domain will show your website’s current security level (medium by default) and some other information.

Before continuing, to get the most out of Cloudflare, you will want to follow this guide: [Recommended First Steps for All Cloudflare Users](https://support.cloudflare.com/hc/en-us/articles/201897700). This is important to ensure that Cloudflare will allow legitimate connections from services that you want to allow, and so that your web server logs will show the original visitor IP addresses (instead of Cloudflare’s reverse proxy IP addresses).

Once you’re all set up, let’s take a look at the **I’m Under Attack Mode** setting in the Cloudflare firewall.

[Step 2 - Enabling **I’m Under Attack Mode**](#step-2-enabling-i-m-under-attack-mode)
-------------------------------------------------------------------------------------

By default, Cloudflare’s firewall security is set to **Medium**. This offers some protection against visitors who are rated as a moderate threat by presenting them with a challenge page before allowing them to continue to your site. However, if your site is the target of a DDoS attack, that may not be enough to keep your site operational. In this case, the **I’m Under Attack Mode** might be appropriate for you.

If you enable this mode, any visitor to your website will be presented with an interstitial page that performs some browser checks and delays the visitor for about 5 seconds before passing them to your server. It will look something like this;

![Interstitial Page](https://assets.digitalocean.com/articles/cloudflare/ddos/11-interstitial-page.png)

If the checks pass, the visitor will be allowed through to your website. The combination of preventing and delaying malicious visitors from connecting to your site is often enough to keep it up and running, even during a DDoS attack.

**Note:** Visitors to the site must have JavaScript and Cookies enabled to pass the interstitial page. If this isn’t acceptable, consider using the “High” firewall security setting instead.

Keep in mind that you only want to have **I’m Under Attack Mode** enabled when your site is the victim of a DDoS attack. Otherwise, it should be turned off so it does not delay normal users from accessing your website for no reason.

### [How To Enable I’m Under Attack Mode](#how-to-enable-i-m-under-attack-mode)

If you want enable **I’m Under Attack Mode**, the easiest way is to go to the Cloudflare Overview page (the default page) and toggle it on in the right sidebar:

![Under Attack Mode action](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_enable_uam.png)

The security settings will immediately switch to **I’m Under Attack** status. Now, any visitors to your site will be presented with the Cloudflare interstitial page that was described above.

### [How To Disable I’m Under Attack Mode](#how-to-disable-i-m-under-attack-mode)

As the **I’m Under Attack Mode** should only be used during DDoS emergencies, you should disable it if you aren’t under attack. To do so, go to the Cloudflare Overview page, and it back off. This will open a modal like this:

![I'm Under Attack disabled](https://deved-images.nyc3.cdn.digitaloceanspaces.com/CTM-739/cloudflare_disable_uam.png)

Then select the security level that you would like to switch to. The default and generally recommended, mode is **Medium**. Your site should revert back to an **Active** status, and the DDoS protection page will be disabled.

[Conclusion](#conclusion)
-------------------------

Now that your website is using Cloudflare, you have another tool to easily protect it against HTTP-based DDoS attacks. There are also a variety of other tools that Cloudflare provides that you may be interested in setting up, like free SSL certificates. As such, it is recommended that you explore the options and see what is useful to you.

You can learn more about using Cloudflare to protect your sites with our [How To Host a Website Using Cloudflare and Nginx on Ubuntu 22.04](https://www.digitalocean.com/community/tutorials/how-to-host-a-website-using-cloudflare-and-nginx-on-ubuntu-22-04) tutorial.
