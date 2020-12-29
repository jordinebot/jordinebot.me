---
title: "5 Things I did to improve my online life"
date: 2020-12-28T16:20:37+01:00
tags: ['internet', 'life', 'lifehacks']
---
As the apparently –and unfortunately– discontinued Firefox's [IRL Podcast](https://irlpodcast.org/) claimed: _"Online Life is Real Life"_, so these are in fact things I put in practice say in the last two years, that definitely made a difference in my day to day.

_Disclaimer: None of the links in this post are affiliated links of any kind nor do I receive anything in exchange for recommending specific products. If I recommend something is just because I use it and I like it._

## Use Firefox and uBlock
I don't really remember when I started using Chrome as my default browser, but knowing myself I'd bet I was an early adopter, so that means I probably used Chrome for almost 10 years. In Nov 14, 2017, Mozilla [announced](https://blog.mozilla.org/blog/2017/11/14/introducing-firefox-quantum/) Firefox Quantum and their marketing people did a pretty good job in convincing me to give it a try. The speed boost and features like the [Enhanced Tracking Protection](https://support.mozilla.org/en-US/kb/enhanced-tracking-protection-firefox-desktop) or the [Multi-Account Containers](https://support.mozilla.org/en-US/kb/containers) made me stay.

Complementing the built-in tracking protection with [uBlock](https://ublockorigin.com/) (also available for Chrome, by the way) makes browsing the Internet a completely different experience. No more annoying banners, no more Youtube pre-rolls and mid-rolls.

## Use a Password Manager and 2FA
My history with passwords is one of many others. At the beginning of times (that'd be around 1997) I had a 6-char [leetspeak](https://en.wikipedia.org/wiki/Leet) password for all my (few) accounts. Starting college they gave me a "strong" 8-char random lowercase password for my `dot edu` e-mail. As more sites where you should sign up appeared, I even used a weaker one (based on some common movement keys in old Spectrum games, older than [WASD](https://en.wikipedia.org/wiki/Arrow_keys#WASD_keys)) for non-important stuff.

Complexity requirements popularised and I started using variations or combinations of my passwords until I ended up with maybe 10 or 12 _not so different_ passwords. Put this on top of a couple of e-mail addresses and you got me using the _I forgot my password_ option at almost every login. So I moved to the [base + suffix](https://luxsci.com/blog/security-simplified-the-basesuffix-method-for-memorable-strong-passwords.html) approach. You would have passwords like `!gr0RTwow_Facebook` or `!gr0RTwow_Twitter`, maybe good enough on their own, but once one has been [owned](https://monitor.firefox.com/) it doesn't require Sherlock Holmes to guess your Gmail one.

Then a couple of years ago I started using [Bitwarden](https://bitwarden.com/) to generate and store all my passwords. I know many people who use the browser's built-in _remember my password_ option, but not so many that generates long random passwords on sign up. To me, the main benefit about a standalone password manager over browser's _remember my password_ are:

- Compatible across browsers and platforms
- You can store any kind of secrets (recovery codes, credit cards, licenses...) not just passwords
- You have access to your passwords everywhere, not just in your devices.
- You can have shared strong passwords with your family (think Netflix, Amazon...)

And I would totally recommend Bitwarden over 1Password, LastPass and other comercial solutions for two reasons mainly:

1. It's [open source](https://github.com/bitwarden)
2. You can [install your own Bitwarden server](https://bitwarden.com/help/article/install-on-premise/) on-premises, if needed

#### Pro-tips
1. Use a strong master ~~password~~ passphrase

[{{< figure caption="Password Strength, by xkcd" src="https://imgs.xkcd.com/comics/password_strength.png" >}}](https://xkcd.com/936/)

2. Use [double-blind passwords](https://kaizoku.dev/double-blind-passwords-aka-horcruxing) for your critical services
3. Enable 2FA where it's available. Prefer an app like [Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) over SMS

## Change sign up e-mails to name+service@domain
I'd say not many people knows that if you own an e-mail address like john.doe@example.com you will also receive any e-mail sent to john.doe+whatever@example.com

Take advantage of this to sign up (or update your current subscriptions) with a different e-mail for every service. If you create an Instagram account, make it john.doe+instagram@example.com. If you subscribe to the New York Times, use john.doe+nytimes@example.com, etc.

This way when you get spam (because you'll always get spam) you'll have a better chance to know who leaked/sold your data.

## Remove social media from your phone
Have you ever thought that you spend (waste) too much time scrolling Instagram's feed? Ever been involved in a stupid Twitter argument that ruined your productivity (and/or your mood)?

If you can relate, I advice to give this a shot: remove the social media apps from your phone. You can go all the way from the beginning or start one by one. I started by removing Facebook (wasn't even using it that much, to be honest) and Twitter, and after a while I was so happy with the results that I removed Instagram as well (also completely deleted my Facebook account).

What to do instead? Always carry a book, for the long waits, listen to podcasts, audiobooks or just enjoy music. Discover some interesting facts, articles and news by checking [Hacker News](https://news.ycombinator.com/news) (not only tech related, regardless of its name) or subscribe to a few newsletters (this seems to be another golden era for them). For starters, I would recommend [Winning the Internet](https://pudding.cool/projects/newsletter/) a cool project by [The Pudding](https://pudding.cool/) that curates links from other newsletters based on their popularity.

## Move away from _free_ critical services
How many times have you read this quote?

> If you are not paying for it, you're not the customer; you're the product being sold

By critical services I mean specially e-mail and cloud storage, and by _free_ I mean mostly Google.

I totally understand that you're ok with _being the product_ in exchange for services that work as well as Gmail. I mean, I've been there. I'm still there, in fact, because I still own a Google Account. But for personal communications or to register to services that I consider critical (banking, password manager, etc.) I have a [Soverin](https://soverin.net/) account with my custom domain.

Something similar happens with my personal files. I don't own a computer at this point and I don't want to store my files in an external drive that will eventually fail. Of course my files include not just personal/family photos, but personal documentation and scans, notes, SSH keys, invoices and other sensitive information. I don't trust Google anymore to permanently keep all of this for me and that's why 3 years ago I moved all my files to [Tresorit](https://tresorit.com/).

Unlike the previous points you don't see a direct impact in your day to day by using Soverin & Tresorit (or probably any other valid alternatives) instead of Gmail & Drive. But I can tell I _feel_ better. 

