# Questions & Answers

> Note: This document documents Q&A during all discussions about [MiniApp Addressing](https://w3c.github.io/miniapp-addressing/) note.

## About syntax-version

### 1. What is the relationship between version in URI and [version.name](https://www.w3.org/TR/miniapp-manifest/#version-name) / [version.code](https://www.w3.org/TR/miniapp-manifest/#code-member) in manifest? (By @hax)

**Answer:** According to the description in the Maniafest spec, `version.name` is a semantic, optional field that can be used by the user agent or developer to show it to the user; and `version.code` is a number that is incremented each time the MiniApp is released. Therefore, the version syntax component in the MiniApp URI can be mapped to the `version.code` in the Manifest proposal.



## About use cases

### 1. Is `location` in [Example 1](https://w3c.github.io/miniapp/specs/uri/#example-1-use-miniapp-uri-in-a-mini-app-1-miniapp-uri) related to the HTML [Location](https://html.spec.whatwg.org/multipage/history.html#the-location-interface) interface?

**Answer:** There is no association. It is recommended that the user agent provides MiniApp URI analysis results in the runtime environment of the MiniApp.



## About security

### 1. Does MiniApp URI has security risks? (by @hax)

**Answer:** The MiniApp URI Scheme may exposes the package address in some situation, which makes someone think that there may be security risk.  However, for the time being, the address of the package is not secret information. That can be obtained easily by capturing the accessing, etc. Therefore, expressing the package address in the URI does not increase the security risk.

For other possible security risks, there are more explanation in the "Security and Privacy" section.

For the security of the entire MiniApp, not only does the provider of the package need to guarantee the security of package transmission, caching, and dereferencing, but also the user agent needs to consider the security of the use of each high-level component or API.



## About accessibility

### 1. What happens when a browser without MiniApp runtime accesses the MiniApp URI?
**Answer:**  If the dereference of the MiniApp URI is not supported, then generally, the browser will let the operating system to identify it. If it cannot be identified, it will not respond or prompt.
And if the developer hopes that the MiniApp can still be called even if the MiniApp URI is running in an unsupported browser, the developer can use some known methods in the industry, such as the deep link, to call up the designated user agent which can recognize and parse MiniApp URIs.




## About dereference

### 1. Why using the HTTPS protocol to download MiniApp package?

**Answer:** There is no requirement on how to download packages. [This chapter](https://w3c.github.io/miniapp/specs/uri/#https) is only a practical way to describe a user scenario. User agents can also download packages using other protocols, or just obtain MiniApp packages locally.



### 2. It seems the "dereferencing" process ends up with a standard HTTP(s) URL. Wouldn't using that as is make it more accessible to end-users? This would trip unless you have a runtime (which would be also presumably, be a browser) - it feels like this tripwire mechanism's main motivation is to make the user install the runtime (in which case, what does this runtime provide that the current browser does not?), which I'm not sure is something that I personally would agree with. (by @cynthia)

**Answer:** I understand your question contains two points,

1. Why not directly use that HTTPS URL as the MiniApp URI?

2. The current MiniApp URI mechanism requires a set of runtimes to parse it. Why is it necessary? What is the difference between that **runtime** and a conventional browser?

For point 1: downloading a package through the network is just one of the ways to get a package. In addition, there are many ways to access a miniapp resource.


For example, after accessing the miniapp once, the miniapp package would be stored locally. Or the user agent can preset the miniapp package before the user accesses the URI for performance (as we mentioned in [§ 2.1.6 of the miniapp white paper](https://w3c.github.io/miniapp/white-paper/#performance-and-user-experience
)). Or when debugging the Miniapp, the developer can directly push the miniapp package to the user agent through a debugging tool.


In these cases, the user agent open miniapp locally according to the appId in MiniApp URI without having to request a package server.

In other cases, the user agent needs to download the package remotely. If the uri is a custom-url, the user agent will download the package from the default package server. If the uri is a https url, the user agent can download the package from the url directly.

In summary, MiniApp URI are designed to locate MiniApp resource cross-platform, regardless of how they are obtained. That cannot be expressed intuitively through HTTPS URLs only. This is why we marked [Chapter 6](https://w3c.github.io/miniapp/specs/uri/#https) non-normative, and just describes it as a user scenario.

For point 2, the runtime make miniapp help fill the gap of the Web and the Native (like we mentioned in [miniapp white paper introduction](https://w3c.github.io/miniapp/white-paper/#what-is-miniapp)). And because the design of the MiniApp is different from traditional web applications (like we mentioned in [miniapp white paper core-features](https://w3c.github.io/miniapp/white-paper/#core-features)). Even if the miniapp packaged is downloaded by a traditional browser, it cannot be opened and run.

And with the necessity of the MiniApp URI demonstrated above, therefore, browsers need to implement MiniApp URI specifications to correctly parse MiniApp URIs and implement MiniApp runtime related specifications (under development) to open and run MiniApps properly.

## About MiniApp Addressing Note

### 1. Why did you change the MiniApp URI scheme to the MiniApp Addressing ?
Answer: This modification is the result of the discussion in [issue #2](https://github.com/w3c/miniapp-addressing/issues/2). The conclusion is that we shouldn't specify a new scheme (`miniapp://`), but rather reuse the HTTP scheme (`https://`), and define a technical solution just like the deep-link. See [#2](https://github.com/w3c/miniapp-addressing/issues/2) for more information.

### 2. Why does this include 2 solutions? Why do we need custom-scheme?
Answer: Because custom-scheme is more compatible with different operating systems. It can run on more systems and devices, and it is also compatible with the existing implementations mechanism(We want the MiniApp vendor to implement this note faster and more securely).

Deep-link technology also contains 2 ways, custom scheme, and HTTP.

On Android, they are called deep-link and app-link respectively.
On iOS, they are referred to as custom URL scheme and universal link respectively.

MiniApp also needs to support both, custom-scheme(like Android deeplink and iOS custom url scheme) and HTTP scheme (like Android applink and iOS universal link).

We prefer user agents to implement HTTP schemes, but custom-scheme is also necessary to appear in this note.

### 3. Why did we specify that 'miniapp' must be included in the URI as uri-infix?
Answer: Unifying the URI syntax of the Minapp, and identifying the URI as the MiniApp URI, which can give developers and users a constant understanding.

Different user agents need to register different schemes (platform://) or HTTP URI prefix(https://platform) in the operating system, so it can’t express the Miniapp character.
A more appropriate way is to use 'miniapp' as the Miniapp character in uri-infix.

### 4. Can user agent(or browser) identify other’s MiniApp URI?
Answer: If the user agent can handle that URI correctly(dereference the URI, download the package, provide runtime, render, and so on) with the standard method, then it can identify other's MiniApp URI and show the corresponding Miniapp. If it can’t, it should open the user agent(or app) which can handle the URI, similar to deep-link.

The ideal situation is that any user agent can handle any MiniApp URI, even if the package comes from a different user agent source. That’s what we are doing at CG and WG, standardizing the MiniApp.

But there is not a unified origin for distributing MiniApps nowadays. MiniApps are managed by package servers of different user agents. And user agents usually don’t allow others to download their packages. So at this stage, it is probably more likely that the user agent open another user agent to handle the others' MiniApp URI.

We hope different vendors can establish a trust mechanism and make packages that can be fetched by each other, just like web app is for browsers.

We can have more discussion about this topic in the [MiniApp Packaging](https://w3c.github.io/miniapp-packaging/) spec.
