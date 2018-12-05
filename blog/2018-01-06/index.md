---
date: "2018-01-06"
title: "Packages are evil!"
category: "Coding"
---

The software supply-chain attacks targeting development ecosystems and package repositories like npm are getting increasingly sophisticated. In the latest incident, an attacker combined social engineering with dependency abuse to backdoor a package with 2 million weekly downloads.

It all started a week ago when someone noticed that a package called flatmap-stream, a dependency of event-stream was injecting some AES-encrypted code. Event-stream is a toolkit that helps developers create and work with streams in Node.js more easily. It is used by almost 1,600 Node packages and gets downloaded around 1.8 million times per week from the npm registry.

Flatmap-stream was added as a dependency to event-stream back in September; not by the original maintainer, but by a user who received publishing rights to the package. It seems that this user, using the handle right9ctrl (now suspended on GitHub and npm), managed to convince the original author, Dominic Tarr, to transfer the package to him after making a few legitimate code contributions.

“He emailed me and said he wanted to maintain the module, so I gave it to him,” Tarr said in a discussion on GitHub. “I don’t get anything from maintaining this module, and I don’t even use it anymore, and haven’t for years.”

People who analyzed the malicious code concluded that it only injected its payload if the environment contained a library called copay-dash that’s part of Copay, a secure Bitcoin and Bitcoin Cash wallet platform for desktop and mobile devices. This means the attack was designed to target only Copay developers, who would have that library in their environments, with the intention of poisoning the official builds of the application that would then be distributed to users.

The final payload injected into Copay was designed to steal users’ private keys for wallets that contained more than 100 Bitcoins or 1,000 Bitcoin Cash and send those keys along with account details to a remote server controlled by hackers.

In conclusion, this was a highly targeted multi-stage attack where one application was compromised through a backdoored dependency higher up in the supply chain. To understand its complexity, here is how the npm security team described the attack chain in a post-mortem analysis:

The injected code:

Read in AES encrypted data from a file disguised as a test fixture;
Grabbed the npm package description of the module that imported it, using an automatically set environment variable;
Used the package description as a key to decrypt a chunk of data pulled in from the disguised file
The decrypted data was part of a module, which was then compiled in memory and executed.
This module performed the following actions:

Decrypted another chunk of data from the disguised file;
Concatenated a small, commented prefix from the first decrypted chunk to the end of the second decrypted chunk;
Performed minor decoding tasks to transform the concatenated block of code from invalid JS to valid JS (we believe this was done to evade detection by dynamic analysis tools);
Wrote this processed block of JS out to a file stored in a dependency that would be packaged by the build scripts.
The chunk of code that was written out was the actual malicious code, intended to be run on devices owned by the end users of Copay.

This code would do the following:

Detect the current environment: Mobile/Cordova/Electron;
Check the Bitcoin and Bitcoin Cash balances on the victim’s copay account;
If the current balance was greater than 100 Bitcoin, or 1000 Bitcoin Cash: Harvest the victim’s account data in full, Harvest the victim’s copay private keys;
Send the victim’s account data/private keys off to a collection service running on 111.90.151.134.
The Copay developers confirmed that versions 5.0.2 to 5.1.0 of the application contained the malicious code. The npm security team took ownership of the event-stream package and removed version 3.3.6 from the registry, as well as the malicious flatmap-stream dependency.

“For npm users, you can check if your project contains the vulnerable dependency by running npm audit,” the team said. “If you have installed the impacted version of this event-stream, we recommend that you update to a later version as soon as possible.”

This incident highlights how hard it is to detect and defend against these attacks, especially in huge ecosystems like npm where there are hundreds of thousands of packages with millions of interdependencies. The malicious code was added in September and was not noticed for two months, more than enough time for hackers to achieve their goals.

The attack also shows that it’s not even necessary to compromise a developer’s machine in order to inject malicious code into a package. Finding devs who are willing, even eager, to give away packages written a long time ago, is probably not that hard. Tarr, for example, is the author of over 400 packages hosted on npm and he’s likely not the only developer who no longer has the time or interest to maintain some of his old creations.

There have also been cases in the past where attackers posed as companies and bought WordPress plug-ins or Google Chrome extensions from their original developers for significant amounts of money. They then added rogue code to those components in order to inject ads into websites or browsing sessions. So paying for access is another option that attackers have.

End users have very few ways to detect such attacks, but developers should make use of all the existing technologies to make life harder for attackers. For example, according to Thomas Hunter II of application security firm Intrinsic, the Copay developers could have taken steps that would have blocked the final payload.

“The attack could have been prevented by making use of CSP (Content Security Policy),” he said in a blog post about the incident. “This is a standard for specifying which URLs a webpage can communicate with and is specified via web server headers. Cordova [a framework used by Copay] even has its own mechanism for specifying which third-party services can be contacted. However, the Copay application appears to have disabled this feature.”

“These supply-chain attacks are only going to become more and more prevalent with time,” Hunter said. “Targeted attacks, like how this package specifically targets the Copay application, will also become more prevalent.”

```md
# asdfasdfads

- auesufuaus
```

```css
code[class*="language-"],
pre[class*="language-"] {
  color: black;
  background: none;
  font-family: Consolas, Monaco, "Andale Mono", "Ubuntu Mono", monospace;
  text-align: left;
  white-space: pre;
  word-spacing: normal;
  word-break: normal;
  word-wrap: normal;
  line-height: 1.5;

  -moz-tab-size: 4;
  -o-tab-size: 4;
  tab-size: 4;

  -webkit-hyphens: none;
  -moz-hyphens: none;
  -ms-hyphens: none;
  hyphens: none;
}
```
