# MiniApp Addressing explainer

> Note: This document serves as a supplementary explanation of the [MiniApp Addressing](https://w3c.github.io/miniapp-addressing/) draft note. If there is any inconsistency with the note, you should consider the note to be authoritative.

## Authors

Dan Zhou, Shuo Wang, Qian Liu, Tengyuan Zhang

## 1. Introduction

### What is this?

MiniApp Addressing defines how MiniApps ([What is MiniApp?](https://w3c.github.io/miniapp/white-paper/#what-is-miniapp)) are located.

Applications including Web applications can use MiniApp Addressing to claim the MiniApp resource it's trying to reference.

### Why should we care?

Each MiniApp vendors, i.e. the MiniApp packaging service providers, are now using very different methods to obtain the MiniApp package. Meanwhile, every user agent has their own way to describe a MiniApp resource. This is creating difficulty to visit a MiniApp across platforms.

For example:

<table>
    <thead>
        <tr class="thead-first-child"><th align="left"> User Agent    </th><th align="left"> URI                                                             </th></tr>
    </thead>
    <tbody>
        <tr class="tbody-first-child"><td align="left"> Wechat App    </td><td align="left"> <code>weixin://dl/&lt;path&gt;/?appid=&lt;appId&gt;&amp;businessType=&lt;businessType&gt;</code> </td></tr>
        <tr class="tbody-even-child"><td align="left"> Alipay App    </td><td align="left"> <code>alipays://platformapi/startapp?appId=&lt;appId&gt;&amp;url=&lt;path&gt;</code>       </td></tr>
        <tr class="tbody-odd-child"><td rowspan="3" align="left"> Quick App     </td><td align="left"> <code>http://hapjs.org/app/&lt;package&gt;/[path][?key=value]</code>             </td></tr>
        <tr class="tbody-even-child"><td align="left"> <code>https://hapjs.org/app/&lt;package&gt;/[path][?key=value]</code>            </td></tr>
        <tr class="tbody-odd-child"><td align="left"> <code>hap://app/&lt;package&gt;/[path][?key=value]</code>                        </td></tr>
        <tr class="tbody-even-child"><td align="left"> Baidu App     </td><td align="left"> <code>baiduboxxapp://swan/appKey/path?key=value</code>                     </td></tr>
        <tr class="tbody-odd-child"><td rowspan="2" align="left"> ByteDance App </td><td align="left"> <code>sslocal://microapp?app_id=&lt;appId&gt;&amp;start_page=pages/home/home</code>  </td></tr>
        <tr class="tbody-even-child"><td align="left"> <code>snssdk143://microapp?app_id=&lt;appId&gt;&amp;start_page=&lt;path&gt;</code>         </td></tr>
    </tbody>
</table>

## 2. Goals

The MiniApp Addressing aims to provide a set of conform syntax rules to concatenate the information of a miniapp, such as id, version, path, so that the user agents can identify, parse, and obtain the miniapp resource on any platform.

The miniapp URI achieve this goal by the following design:

* Identify the miniapp package resource by its host, id and version components. In most cases, through a custom-scheme or host, a user agent can discover a miniapp package management service, then access a specific miniapp package by providing the unique ID and version of the miniapp.
* Identify the resource inside miniapp package by its path, query and fragment components. The definition of these three components is close to a HTTP url in browser. How those components can be used relies on other miniapp specs, especially, the [MiniApp Packaging specification](https://w3c.github.io/miniapp/specs/packaging/).

### Key Considerations

#### Why is a custom scheme necessary?
Currently, most of MiniApp user agents use deep-link technology to open a MiniApp.

Deep-link technology contains 2 ways, custom scheme, and HTTP.

On Android, they are called app-link and deep-link respectively.
On iOS, they are referred to as custom URL scheme and universal link respectively.

MiniApp also needs to support both, custom-scheme(like Android deeplink and iOS custom url scheme) and HTTP scheme (like Android applink and iOS universal link).

We prefer user agents to implement HTTP schemes, but custom-scheme is also necessary to appear in this note.

What's more, custom-scheme is more compatible with different operating systems. It can run on more systems and devices, and it is also compatible with the existing implementations mechanism(We want the MiniApp vendor to implement this note faster and more securely).


#### Where does id come from?

Package management service can use some algorithm to generate a universally unique id. This process happens when a miniapp is registered.

#### Why is version components needed?

When a user visits a web page, the page content is always up to date. This is the same for miniapp in most cases, so the version in miniapp URI usually is null. But in some cases, a user agent or user needs to access some specific versions of miniapp. So we reserve the version component.

## 3. Non-goals

The following titles are also important but out of the scope of MiniApp Addressing:

1. the identifier of the resource within a miniapp package, which may include path, query, fragment (this may be defined in the [packaging specification](https://w3c.github.io/miniapp/specs/packaging/));
2. the storage and management of a miniapp package;
3. how to generate the id of a miniapp package;
4. the rule to define a version number;
5. how the Web developers obtain the id and version of a miniapp package;
6. how to obtain a miniapp package (we just provide a suggested way to obtain the package as [an use case](https://w3c.github.io/miniapp/specs/uri/#https)).

## 4. Key scenarios

### Scenario 1 A link to a miniapp in a web page

<p><img width="500px" src="https://user-images.githubusercontent.com/12129112/123224358-5524e980-d504-11eb-8c51-4179236a4055.png" alt="figure1. the use case which link to a miniapp in a web page" /></p>

Example code1:

```html
<!doctype html>
<html>
<a href="platform://miniapp/foo;version=1.0.1-trial/pages/index?k=v#bar">open a MiniApp</a>
</html>
```

`platform` is a miniapp platform identifier to uniquely identify a user agent on a mobile device, which can be other string, usually registered in the operating system (say mobile deep linking technology)


Example code2:

```html
<!doctype html>
<html>
<a href="https://platform.org/miniapp/foo;version=1.0.1-trial/pages/index?k=v#bar">open a MiniApp</a>
</html>
```

`https://platform.org` also can uniquely identify a user agent on a mobile device, used by deep linking technology.

Browsers may handle the click action of Link A inconsistently for this Web page.

* If it is running in a web page or MiniApp page or Native App that has a miniapp runtime, and the user agent recognize its scheme and enable download the miniapp package, the URI can be parsed properly, and it will be retrieved  the resource from the package management service, then locate the URI path, query, fragment and other information to dereference the corresponding resource.
* If it is in a web page of or Native App that does not have a miniapp runtime or does not have privilege to download the miniapp package, the platform can parse the URI properly but can not run the miniapp resource. To provide a smooth user experience, it may trigger other user agent to open the miniapp.

### Scenario 2 Use MiniApp URI within a miniapp

Similar to parsing the URLs of each parts of the context for a web page, in MiniApp's runtime context, developers also need to know all the necessary information of the URI corresponding to the current MiniApp page. These information may include,

Example code1:
```javascript
console.log(location.href);     // platform://miniapp/foo;version=1.0.1-trial/pages/index?k=v#bar
console.log(location.protocol); // platform:
console.log(location.urifix); // miniapp    (always be miniapp)
console.log(location.origin);   // platform://miniapp/foo;version=1.0.1-trial
console.log(location.id);       // foo
console.log(location.version);  // 1.0.1-trial
console.log(location.host);     // ''
console.log(location.pathname); // /pages/index
console.log(location.search);   // ?k=v
console.log(location.hash);     // #bar
```

Example code2:
```javascript
console.log(location.href);     // https://platform.org/miniapp/foo;version=1.0.1-trial/pages/index?k=v#bar
console.log(location.protocol); // https:
console.log(location.urifix); // miniapp    (always be miniapp)
console.log(location.origin);   // https://platform.org/miniapp/foo;version=1.0.1-trial
console.log(location.host);     // platform.org
console.log(location.id);       // foo
console.log(location.version);  // 1.0.1-trial
console.log(location.pathname); // /pages/index
console.log(location.search);   // ?k=v
console.log(location.hash);     // #bar
```

More use case of MiniApp can be found in the [MiniApp White Paper use case](https://w3c.github.io/miniapp/white-paper/#case-studies).

## 5. Security and Privacy Considerations

### Security

1. Similar to other URLs, MiniApp URI may expose the host and port of a miniapp package, so there may be a risk of being scanned and attacked for the other hidden ports. Service providers are suggested to avoid the risks through authentication or other efficient means.
2. When the UA reads and dereferences the URI, there maybe security risks such as injection, hijacking, and man-in-the-middle attacks during the requests to obtain the package from the service provider. User agents are suggested to send requests using HTTPS and data encryption. Packet integrity and security validation can also help to identify and terminate abnormal URI access to tampered package files.
3. When the user agent stores the package resource locally, it is necessary to ensure the storage security of files and isolate them properly.

### Privacy

1. User Agent should isolate the resources of the local package. Only the current miniapp can cache or access these resources, while other miniapp or applications are not supposed to. In addition, the user agent needs to obfuscate the resource names and provide mapping for the paths of the stored files, to prevent file privacy leaks.
2. User agent should restrict the permissions of the miniapp to access certain information, miniapp can only access the user's private information (such as user address, mobile number, or email) after the user has authorized.

## 6. Detailed design discussions

The main form of discussion are meetings and offline communication. All of the discussion has been recorded in [the Q&A document](./Q&A.md).

## References & acknowledgements

In the process of writing the MiniApp URI specification, many people have given valuable comments, thank you all.

The following name list will be continuously updated, in the order of alphabetical.

* Hax
* Langyu Liu
* Jing Huang
* Ming Zu
* Wei Sun
* Xiaohong Deng
* Xiaoqian Wu
* Yuan Lu
* ...
