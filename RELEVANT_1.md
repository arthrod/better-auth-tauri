# How to ensure callbackUrl work

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
