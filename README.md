# How to Set Up Your Cloudflare Account for Your Framer

## Step 1: Set up your Cloudflare account (5 mins)

1. Sign up for an account: [Cloudflare Sign-Up](https://dash.cloudflare.com/sign-up)
2. Enter your custom domain name. If you would like to use a subdomain, you should still enter your root domain name here.
3. Select the Free plan.
4. If you don't have any A records imported, add one with your root domain as the Name and 1.1.1.1 as the Content. Otherwise, click Continue on the DNS Record page.
   - If you are using a subdomain, add one with your subdomain as the Name and 1.1.1.1 as the Content.
5. Copy the 2 nameservers, which end with .ns.cloudflare.com.
6. Paste the nameservers in the domain setting page at your registrar (Namecheap in my case). Make sure you save the setting.
7. Wait for a minute, then click Done, check nameservers.
8. Select Flexible SSL/TLS encryption mode.
9. Turn on Always Use HTTPS, Auto Minify, and Brotli (all 3 optional but recommended).
10. Select Done.
11. You should see a confirmation screen. If Cloudflare hasn't detected your site, click Re-check your site, and refresh the page.
12. Select the Workers page (one of the blue boxes) and then click Manage Workers.
13. Choose any available subdomain for your worker (it doesn't really matter what you pick).
14. Click Set Up and then click Confirm.
15. Choose the Free plan.
    - If your site gets a lot of visitors, you can change to the paid Unlimited plan later.
16. Verify your email if you haven't, then go back to the Manage Workers page.
17. Click Create a Worker.

## Step 2: Customize and generate the script (2 mins)
```
export default {
  async fetch(request, env, ctx) {
    try {
      // Step 1: Capture the Request Path
      const url = new URL(request.url);
      const path = url.pathname; // This gets the path of the request      

// Step 2: Construct the Target URL
      const targetUrl = `https://yoururl.webflow.io${path}`;      

// Step 3: Fetch the Page
      const response = await fetch(targetUrl);
      let content = await response.text(); // Assuming the content is HTML      

//Show this file to remove the framer tag
const cssToHideBadge = `<style>#__framer-badge-container { display: none !important; }</style>`;
      content = content.replace('</head>', `${cssToHideBadge}</head>` );

// Step 4: Respond with the Page
      return new Response(content, {
        headers: {"Content-Type": "text/html"} // Ensure proper content type
      });
    } catch (e) {
      // Handle any errors
      return new Response('An error occurred: ' + e.message, { status: 500 });
    }
  }
};
```

## Step 3: Paste the script in Cloudflare (1 min)

1. Delete the existing code, and paste the code you copied.
2. Click Save and Deploy.
3. After saving, click on your site name on the top of the page.
4. Go to the Workers page and select Add Route.
