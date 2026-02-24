# Why we don't need Bearer Token for Authentication with Better-Auth

<user>Am I understanding correctly that since the scheme based Callback URL is only being appended for social providers, only social login works with Tauri?

better-auth-tauri/src/plugin/append-callback-url.ts

Line 21 in 390fc6c
 Object.keys(ctx.context.options.socialProviders).forEach((key) => { </user>

<maintainer>
daveycodez commented on Sep 29, 2025
daveycodez
on Sep 29, 2025
Contributor

This is actually just a hack for Google. Magic Link should work just fine! Just put your scheme:// in callbackURL
daveycodez
daveycodez commented on Sep 29, 2025</maintainer>

<user>khakra
khakra commented on Sep 29, 2025
khakra
on Sep 29, 2025
Author

that'd be great, thanks for building this!

As a side note,

    I was wondering if using the bearer plugin and storing the bearer token might be a better approach for Tauri apps as opposed to storing cookies. Or JWT?

    I tried poking around Notion and Wisprflow to see how they authenticate, especially for social auth. I found that Wisprflow opens the social auth link in default browser and at the end of it, displays a button to open deep link. This button then sends the Google refreshToken / authToken to the MacOS app as part of this Deeplink. Do you think this might be easier / better? The advantage of opening social link in user's browser is users can reuse logged-in session as opposed to typing their Google email / password all over again (no password manager either).

What are your thoughts? If you need any help, I'd be happy to collaborate with you in this implementation.</user>
<maintainer>daveycodez
daveycodez commented on Sep 29, 2025
daveycodez
on Sep 29, 2025
Contributor

This is actually already set up to open in the default browser using the opener plugin. You just need to use the signInSocial function from this package.

The only time it’ll open in the app window is macOS dev mode which is just a container for your local host anyway and doesn’t support deep links. For macOS you need to make a production build and drag it to the applications folder
daveycodez
daveycodez commented on Sep 29, 2025
daveycodez
on Sep 29, 2025
Contributor

As for bearer, it’s not necessary at all because we use the Tauri HTTP Plugin now. I think I need to update the readme. But the Tauri HTTP plugin allows us to circumvent CORS and get cookies normally</maintainer>
