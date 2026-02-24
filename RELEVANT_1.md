# How to ensure callbackUrl work

## The solution below fixes the incorrect representation

<solution>Thought for 1m 11s
Now I can see the full flow. The issue is clear:
1. First callback (browser): callbackURL=fukurou:// V - middleware intercepts, redirects to success page
2. Success page triggers deep link: fukurou://ap1/auth/callback/google?callbackURL=82F&…•
3. Tauri receives deep link and handleAuthDeepLink makes HTTP request to server with callbackURL= 2F
4. Server processes Auth, sets cookie, returns 302 redirect to /
5. Tauri HTTP client follows the redirect to GET / → 404
The bug is that when handleAuthDeepLink makes the HTTP request to process the Auth callback, the server redirects to /
and the Tauri HTTP client follows it. Since / doesn't exist on your server, you get a 404.
There are a few ways to fix this:
Option 1: Add a catch-all or root route on your server that returns 200 OK (simplest)
Option 2: Change callbackURL in the tauri plugin to a path that exists on your server
Let me check what server you're using to add a simple handler.

By default, your callbackUrl is your scheme, so elysium:// in your case.
But you notice in your logs that it later changes to /.
Which makes the final call to your backend, which sets your cookies, redirect to the / endpoint on your server.
If you have one, it works great. If you don't have one (like I did), it fails spectacularly.
jannisbecker
jannisbecker commented on Jan 15
jannisbecker
on Jan 15

The solution is simply to add an endpoint like

app.get("/", (c) => {
	return c.json({ status: "OK" });
});

I encourage the maintainer to document this somewhere, it was truly painful to figure out
</solution>

## Incorrect representation
<incorrect_representation>
- This is incorrect read solution above please: Hey. Whenever I try to log in this flow happens:
- This is incorrect read solution above please: Login -> Discord Oauth Flow -> Success Page -> Redirect to Tauri -> Callback to localhost:1420/404
- This is incorrect read solution above please: Meaning everything stays in the same page and no refresh is forced, thus my application not recognizing the logged in state. However once I force a reload or restart my application, everything works as expected. It's just the last callback.
- This is incorrect read solution above please: I followed the Svelte Setup Guide from the README.md 1:1 while replacing the schema etc.
- This is incorrect read solution above please: Logs
- This is incorrect read solution above please: Browser Logs:
- This is incorrect read solution above please: [Log] [Better Auth Tauri] handleAuthDeepLink fetch – "/callback/discord?callbackURL=%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds" (@daveyplate_better-auth-tauri.js, line 2760)
- This is incorrect read solution above please: [Log] Auth request: – "/callback/discord?callbackURL=%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds" (+layout.svelte, line 33)
- This is incorrect read solution above please: [Error] Failed to load resource: the server responded with a status of 404 (Not Found) (localhost, line 0)
- This is incorrect read solution above please: [Log] [Better Auth Tauri] handleAuthDeepLink response (2) (@daveyplate_better-auth-tauri.js, line 2765)
- This is incorrect read solution above please: {data: null, error: {status: 404, statusText: "Not Found"}}
- This is incorrect read solution above please: "/callback/discord?callbackURL=%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds"
- This is incorrect read solution above please: [Error] [Better Auth Tauri] handleAuthDeepLink error (2)
- This is incorrect read solution above please: {status: 404, statusText: "Not Found"}
- This is incorrect read solution above please: "/callback/discord?callbackURL=%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds"
	- This is incorrect read solution above please: handleAuthDeepLink (@daveyplate_better-auth-tauri.js:2773)
- This is incorrect read solution above please: [Error] Auth error: – {status: 404, statusText: "Not Found"}
	- This is incorrect read solution above please: onError (+layout.svelte:42)
	- This is incorrect read solution above please: handleAuthDeepLink (@daveyplate_better-auth-tauri.js:2779)
- This is incorrect read solution above please: 
- This is incorrect read solution above please: API Logs:
- This is incorrect read solution above please: 
- This is incorrect read solution above please: Started development server: http://localhost:3000
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Request URL: http://localhost:3000/auth/sign-in/social User Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/60.5 Safari/605.1.15 Host: - This is incorrect read solution above please: localhost:3000 Pathname: /api/auth/sign-in/social
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Appending callback URL to social provider discord http://localhost:3000/auth/callback/discord?callbackURL=elysium://
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Callback URL: null /api/auth/sign-in/social
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Request URL: http://localhost:3000/auth/callback/discord?callbackURL=elysium%3A%2F%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds User Agent: Mozilla/5.0 - This is incorrect read solution above please: (X11; Linux x86_64; rv:145.0) Gecko/20100101 Firefox/145.0 Host: localhost:3000 Pathname: /api/auth/callback/discord
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Callback URL: elysium:// /api/auth/callback/discord
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Redirecting to: elysium://api/auth/callback/discord?callbackURL=%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds /api/auth/callback/discord
  - This is incorrect read solution above please: api:dev | [Better Auth Tauri] Request URL: http://localhost:3000/auth/callback/discord?callbackURL=%2F&code=LNhnvcQ0cVqJ9NKhmGQdeS4nk1lu1z&state=wplr2W88dpjoNiKVlFgx2xXXAeg143ds User Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/605.1.15 (KHTML, like Gecko) Version/60.5 Safari/605.1.15 Host: localhost:3000 Pathname: /api/auth/callback/discord
  api:dev | [Better Auth Tauri] Callback URL: / /api/auth/callback/discord</incorrect_representation>incorrect_representation>
