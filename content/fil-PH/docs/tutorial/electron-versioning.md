# Pagbebersyon ng Electron

> Isang detelyadong pagtanaw sa ating pagbebersyon na mga patakaran at implementasyon.

Sa ngayon ang bersyon ng 2.0.0, Electron ay sumusunod sa [semver](#semver). Ang mga sumusunod na command ay mag-iinstall ng mga pinakabago at matatag na build ng Electron:

```sh
npm install --save-dev electron
```

Upang maka-update ng umiiral na proyekto para makagamit sa matatag at pinakabagong bersyon:

```sh
npm install --save-dev electron@latest
```

## Bersyon 1.x

Ang mga bersyon ng Electron *< 2.0* ay hindi naaayon sa [semver](http://semver.org) na spesifikasyon. Malaking bersyon na magkatugon sa mga huling-tagagamit ng pagbabago sa API. Ang mga maliliit na bersyon ay nakatugon sa mga malaking release ng Chromium. Ang mga bersyon ng patch ay nakatugon sa mga bagong tampok at pagreresolba ng mga di kanais-nais na bug. Madali lang para sa mga developers ang pagsasama ng mga katangian, ngunit ito'y nagbibigay ng porblema sa mga developer ng client-facing na mga aplikasyon. Ang pagsusuri sa QA ng mga pangunahing aplikasyon gaya ng Slack, Stride, Teams, Skype, VS Code, Atom, at Desktop ay maaring napakahaba at ang katatagan ay isang tanging hinahangad na resulta. Ngunit mayroong napakapanganib na resulta sa paggamit ng mga bagong katangian para maresolba ng mga bugs na problema.

Isang halimbawa ng 1.x na istratehiya ay:

![](../images/versioning-sketch-0.png)

Ang app na binuo sa `1.8.1` ay hindi makukuha sa `1.8.3` pagsasaayos ng mga bugs na hindi gumagamit ng `1.8.2` na katangian, o sa pagba-backport ng pagsasaayos at pagpapanatili sa linya ng mga bagong labas.

## Bersyon 2.0 at Pataas

May mga ilang malaking pagbabago mula sa ating 1.x na stratehiya na nakabalangkas sa ibaba. Bawat pagbabago ay dapat matugunan ang mga pangangailangan at prayoridad ng mga taga-likha o taga-maintain at sa mga tagalikha ng mga aplikasyon.

1. Striktong paggamit ng semver
2. Pambungad ng semver-compliant `-beta` tags
3. Pambungad sa [conventional commit messages](https://conventionalcommits.org/)
4. Malinaw na pagpapaliwanag sa pagpapanatag ng mga sangay
5. Ang `master` na branch ay walang bersyon; tanging ang katatagan ng branches lamang ang naglalaman ng mga impormasyon tungkol sa bersyon

Tatalakayin natin bawat detalye kung paano gumagana ang git branching, pati ang nmp tagging, at kung ano ang aasahang makikita ng mga developers, at kung paano ka makapagbabago ng backport.

# semver

Mula sa 2.0 pataas, ang Electron ay sususnod sa semver.

Ang nasa ibaba ay isang talahanayan ng iba't-ibang uri ng pagmamapa sa mga pagbabago sa nararapat na kategorya ng semver (hal. Major, Minor, Patch).

* **Mga karagdagan sa Major na Bersyon** 
    * Mga updates sa Chromium version
    * malaking updates sa node.js
    * Ang pagpapalit ng API dahil sa pagsisira ng Electron nito
* **Mga karagdagan sa Minor na Bersyon** 
    * mga maliit na updates sa node.js
    * Pagpapalit ng API ng hindi lumalabag sa Electron
* **Mga karagdagan sa Patch na Bersyon** 
    * mga updates sa bersyon ng node.js patch
    * pagsasaayos ng may kaugnayan sa chromium patches
    * ang electron ay nagsasaayos ng mga bug

Tandaan na karamihan sa chromium updates ay itinuturing nakakasira. Ang pagsasaayos na pwedeng i-backport ay maaring maging cherry-picked na patches.

# Pagpapapanatag ng mga Branch

Ang pagpapapanatag ng mga branch ay mga branch na tumatakbo kahilera sa master, sa pagkuha lang ng cherry-picked commits na may kinalaman sa seguridad at katatagan. Ang mga branch na ito ay hindi na masasama ulit sa master.

![](../images/versioning-sketch-1.png)

Ang pagpapanatag ng branches ay palaging linya sa bersyon ng alinman sa **major** o **minor**, at may pangalan laban sa mga sumusunod na template `$MAJOR-$MINOR-x` e.g. `2-0-x`.

Pumapayag kami sa maramihang pagpapanatag ng mga branches para umiral nang sabay-sabay, at tangkain na suportahan kahit sa dalawang magkahanay sa lahat ng oras, ang pagsasaayos ng seguridad sa pagba-backport na kinakailangan. ![](../images/versioning-sketch-2.png)

Ang mga lumang linya ay hindi suportado ng GitHub, pero pwedeng may ibang grupo na magmamay-ari at magba-backport ng katatagan at seguridad na pwedeng umayos sa kanilang sarili. Hindi namin ito minumungkahi, pero kinikilala namin ito dahil ito'y nagpapadali sa buhat ng maraming app developers.

# Beta Releases at ang pagsasaayos ng Bug

Gustong malaman ng mga developers kung aling release ang mga *ligtas* gamitin. Kahit mga bagong tampok ay tila maaaring ipakilala ang pagbabalik sa mga kumplikadong applications. Kasabay nito, ang pagla-lock sa isang permanenteng bersyon ay delikado dahil ikaw ay nagbabalewala sa seguridad ng patches at pagsasaayos ng bug naa maaring dumating sa iyong bersyon. Ang hangad namin ay upang payagan ang mga sumusunod na pamantayan ng semver na saklaw ng `package.json` :

* Gumamit ng `~2.0.0` para aminin lamang ang katatagan o seguridad katulad ng pagsasaayos sa `2.0.0` na release.
* Gumamit ng `^2.0.0` para aminin ang di paglabag sa *matatag na katwiran* sa tampok na trabaho pati na rin ang seguridad at pagsasaayos ng mga bug.

What’s important about the second point is that apps using `^` should still be able to expect a reasonable level of stability. To accomplish this, semver allows for a *pre-release identifier* to indicate a particular version is not yet *safe* or *stable*.

Whatever you choose, you will periodically have to bump the version in your `package.json` as breaking changes are a fact of Chromium life.

The process is as follows:

1. All new major and minor releases lines begin with a `-beta.N` tag for `N >= 1`. At that point, the feature set is **locked**. That release line admits no further features, and focuses only on security and stability. e.g. `2.0.0-beta.1`.
2. Bug fixes, regression fixes, and security patches can be admitted. Upon doing so, a new beta is released incrementing `N`. e.g. `2.0.0-beta.2`
3. If a particular beta release is *generally regarded* as stable, it will be re-released as a stable build, changing only the version information. e.g. `2.0.0`.
4. If future bug fixes or security patches need to be made once a release is stable, they are applied and the *patch* version is incremented accordingly e.g. `2.0.1`.

For each major and minor bump, you should expect too see something like the following:

```text
2.0.0-beta.1
2.0.0-beta.2
2.0.0-beta.3
2.0.0
2.0.1
2.0.2
```

An example lifecycle in pictures:

* A new release branch is created that includes the latest set of features. It is published as `2.0.0-beta.1`. ![](../images/versioning-sketch-3.png)
* A bug fix comes into master that can be pack-ported to the release branch. The patch is applied, and a new beta is published as `2.0.0-beta.2`. ![](../images/versioning-sketch-4.png)
* The beta is considered *generally stable* and it is published again as a non-beta under `2.0.0`. ![](../images/versioning-sketch-5.png)
* Later, a zero-day exploit is revealed and a fix is applied to master. We pack-port the fix to the `2-0-x` line and release `2.0.1`. ![](../images/versioning-sketch-6.png)

A few examples of how various semver ranges will pick up new releases:

![](../images/versioning-sketch-7.png)

# Missing Features: Alphas, and Nightly

Our strategy has a few tradeoffs, which for now we feel are appropriate. Most importantly that new features in master may take a while before reaching a stable release line. If you want to try a new feature immediately, you will have to build Electron yourself.

As a future consideration, we may introduce one or both of the following:

* nightly builds off of master; these would allow folks to test new features quickly and give feedback
* alpha releases that have looser stability constraints to betas; for example it would be allowable to admit new features while a stability channel is in *alpha*

# Feature Flags

Feature flags are a common practice in Chromium, and are well-established in the web-development ecosystem. In the context of Electron, a feature flag or **soft branch** must have the following properties:

* is is enabled/disabled either at runtime, or build-time; we do not support the concept of a request-scoped feature flag
* it completely segments new and old code paths; refactoring old code to support a new feature *violates* the feature-flag contract
* feature flags are eventually removed after the soft-branch is merged

We reconcile flagged code with our versioning strategy as follows:

1. we do not consider iterating on feature-flagged code in a stability branch; even judicious use of feature flags is not without risk
2. you may break API contracts in feature-flagged code without bumping the major version. Flagged code does not adhere to semver

# Semantic Commits

We seek to increase clarity at all levels of the update and releases process. Starting with `2.0.0` we will require pull requests adhere to the [Conventional Commits](https://conventionalcommits.org/) spec, which can be summarized as follows:

* Commits that would result in a semver **major** bump must start with `BREAKING CHANGE:`.
* Commits that would result in a semver **minor** bump must start with `feat:`.
* Commits that would result in a semver **patch** bump must start with `fix:`.

* We allow squashing of commits, provided that the squashed message adheres the the above message format.

* It is acceptable for some commits in a pull request to not include a semantic prefix, as long as a later commit in the same pull request contains a meaningful encompassing semantic message.

# Versionless `master`

* The `master` branch will always contain `0.0.0-dev` in its `package.json`
* Release branches are never merged back to master
* Release branches *do* contain the correct version in their `package.json`