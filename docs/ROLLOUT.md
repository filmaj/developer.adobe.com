# Operational Rollout, Monitoring and Escalation

This is a [rollout plan](#rollout-phases) that details the path to go-live for the
helix-powered version of the site and includes sections around monitoring and escalation.
The rollout will use a progressive delivery approach that allows incremental, risk-minimizing
updates with solid automation.

**Many aspects of this plan are yet to be implemented**. Where applicable, such
*TODOs* are linked to open issues or pull requests.

1. [Monitoring](#monitoring)
    - [Uptime](#uptime)
    - [Implementation](#implementation)
    - [Services We Rely On](#services-we-rely-on)
2. [Fallback vs. Rollback](#fallback-vs-rollback)
3. [Escalation](#escalation)
    - [Legacy Escalation Process](#legacy-escalation-process)
    - [Responding to Incidents](#responding-to-incidents)
    - [Runbooks](#runbooks)
4. [Rollout Phases](#rollout-phases)
    - [Phase 1: Everything still looks the
        same](#phase-1-everything-still-looks-the-same)
    - [Phase 2: New Open page for some](#phase-2-new-open-page-for-some)
    - [Phase 3: New Open page for more](#phase-2-new-open-page-for-more)
    - [Phase 4: New Open page for all](#phase-2-new-open-page-for-all)
    - [Phase 5.1: New Product Landing and Documentation
        Pages](#Phase-5-1-new-product-landing-and-documentation-pages)
    - [Phase 5.2: New Product Landing and Documentation Pages For Everyone](#Phase-5-1-new-product-landing-and-documentation-pages-for-everyone)
    - [Phase 6: Home page](#phase-6-home-page)
    - [Phase 6: More Product Pages](#phase-6-more-product-pages)
    - [Phase 6: New Product Pages](#phase-6-new-product-pages)

## Monitoring

### Uptime

The requirement for uptime is 99.99% which allows for 52 minutes and 36 seconds
of downtime per year.

A "publishing failure" should be considered as downtime. Time-to-live should be
taken into account and clearly documented for content repository updates, but
this time-to-live (TTL) should be treated as a service-level agreement between us
(the site) and our customers (content authors and consumers).

### Implementation

The existing monitoring procedures that are used for the AEM-based site stay in
place. As the URLs don't change and monitoring is technology insensitive, this
enables us to have the same level of quality as before. As we phase out using
sections of the AEM-based site, we must ensure monitoring is rolled out as well.
That is, monitoring *must* be part of the definition of done for helix-powered
sections of the site.

Helix provides a [status page](https://status.project-helix.io/) that should be
used to identify helix service degradation.

*TODO*: to be implemented
([adobe/developer.adobe.com-planning#172](https://github.com/adobe/developer.adobe.com-planning/issues/172))

#### Services We Rely On

- helix
- https://medium.com/feed/adobetech

## Fallback vs. Rollback

As a quick fix for most problems, we will establish [Fallback](#fallback) and [Rollback](#rollback)
procedures that can alleviate errors until a more permanent fix has been provided.

### Fallback

While we are running AEM and helix in parallel, we will have two versions of the
site available. Fallback is defined as directing traffic from one source to
another.

To enable fallback in a helix-powered reality, we turn off the strain(s) to the
affected area(s) of the site so that the `default` (proxy to existing AEM site)
gets applied, showing the "old" version of the site.

*TODO*: How does this implementation look like? Can we enshrine as an `npm run`
script?

### Rollback

Rollback is defined as going back to a known-good previous version. In a
helix-powered reality this means rolling back to a previous commit and running a
deployment. Note that this is distinct from [fallback](#fallback) in that the
AEM-powered version of the site does not come into play.

Perform a `hlx publish` using an older `helix-config.yaml` that has been committed
as part of an older, successful rollout. This will replace the entire site with
a slightly older version.

*TODO*: How does this implementation look like? Can we enshrine as an `npm run`
script?

## Escalation

*TODO*: helix is figuring out its escalation process, so the below is in much
flux until [adobe/project-helix#382](https://github.com/adobe/project-helix/pull/382)
is resolved.

The [existing escalation paths](#legacy-escalation-process) stay in place, with
one modification: **For every escalation, the Helix team will be involved**.

### Legacy Escalation Process

Currently, we get notifications and alerts from AMS on P2 priority issues and they
are addressed by the website team where we engage the AEM support team as needed.

For any P1 issue, AMS will engage the support team, NOC and all listed stakeholders
are notified of the issue and a war room is opened as needed.

### Responding to Incidents

*TODO*: helix is figuring out its escalation process, so the below is in much
flux until [adobe/project-helix#382](https://github.com/adobe/project-helix/pull/382)
is resolved.

The Helix team is providing and monitoring the `#helix-escalations` Slack channel.
Every message in the `#helix-escalations` Slack channel will create a notification
and wake people up, so use this channel with caution!

Any incident involving Fastly, GitHub or Adobe Runtime must involve the Helix
team.

In the beginning, this should even be done for escalations that only affect the
existing AEM infrastructure, so that the Helix team can develop procedures that
allow a quick assertion if an issue is caused by:

1. strains running on Project Helix
2. or issues in the strain resolution and/or proxying itself
3. or strains running as proxy strains, using the existing stack

In the third case, the Helix team will be only be able to provide limited assistance.

Between the helix and website teams we have pretty good global timezone coverage
(with a potential single-point-of-failure being @tripodsan as he pretty much
covers all Asian timezones himself!).

During work hours, @trieloff expects a response time of a few minutes.
Early afternoon during American hours may be problematic if helix team involvement
is necessary as it will be evening in Europe and sleeptime in Asia.

*TODO*: On-call support from helix is evolving as per
[adobe/project-helix#382](https://github.com/adobe/project-helix/pull/382)

### Runbooks

The dev site team will maintain a set of operational runbooks, or incident
handling instructions, that should evolve over time with each incident. There is
plenty of prior art in this area, with inspiration available at:

- The [open GitLab runbooks](https://gitlab.com/gitlab-com/runbooks)
- PagerDuty's [Getting Started guide](https://response.pagerduty.com/getting_started/)
    which has details on how to set up incidence handling in your own
    organization.

*TODO*: These will be written as incidents are dealt with. Such runbooks should
include referencing [fallback](#fallback) and [rollback](#instructions) as "last
ditch" maneuvers. They should be linked here.

## Rollout Phases

### Phase 1: Everything still looks the same

The existing site will be served though a Helix proxy strain, so that no visible
changes have been made to the site, but Helix has the ability to manage the site.

#### Prerequisites

- [ ] `helix-config.yaml` with a `default` strain pointing to the existing AEM
    site's dispatcher
    ([adobe/developer.adobe.com-planning#162](https://github.com/adobe/developer.adobe.com-planning/issues/162))
- [ ] Fastly has certificate for `www.adobe.io` domain
    ([adobe/developer.adobe.com-planning#167](https://github.com/adobe/developer.adobe.com-planning/issues/167))
- [ ] Fastly configuration receives traffic for `www.adobe.io` domain
    ([adobe/developer.adobe.com-planning#168](https://github.com/adobe/developer.adobe.com-planning/issues/168))
- [ ] CircleCI is set up to run `hlx publish` on `master`
    ([adobe/developer.adobe.com-planning#169](https://github.com/adobe/developer.adobe.com-planning/issues/169))
- [ ] GitHub `master` branch is protected from accidental merges

### Phase 2: New Open page for some

The [Open page](https://www.adobe.io/open.html) will be powered by helix and made
available to visitors in a restricted geography (e.g. Switzerland) (work tracked
in ).
All other visitors will still see all other pages powered by AEM (based on the
default proxy strain).

Switzerland is selected as a target region, so that:
1. Potential exposure of bugs is limited to a small population
2. Escalations are most likely to happen during Swiss working hours, ensuring availability
    of the Project Helix team for troubleshooting

**Note:** the goal of this staged rollout is to provide a stable experience for
the majority of visitors, with the ability to expand the new Open page experience
to greater visitor groups once it has been validated.

Because the new experience is only for the Open page, redirects don't have to
be migrated yet, as there are no redirects associated with the Open page on AEM.

#### Prerequisites

- [ ] a `open-geo-ch` strain pointing to the new site
- [ ] a finished Open page implementation ([adobe/developer.adobe.com-planning#165](https://github.com/adobe/developer.adobe.com-planning/issues/165))
- [ ] a basic functional test for the new Open page
- [x] Helix team on standby in `#helix-escalations` Slack channel

### Phase 3: New Open page for more

The new Open page will be made available to visitors in a wider, but still restricted
geography (all of Europe). All other visitors will still see the old Open page based
on the default proxy strain.

#### Prerequisites

- [ ] stakeholders informed in advance
- [ ] a `open-geo-europe` strain pointing to the new site
- [ ] a `open-stage-test` strain pointing to the new site for end to end testing
- [ ] basic end-to-end test for the new Open page, running in production against
    the `open-stage-test` strain
- [ ] CircleCI set up to deploy to `open-stage-test`, perform the test and continue
    with full publishing only when the test succeeds

*TODO*: the above testing and stage environment details still need to be ironed out in [adobe/developer.com.adobe-planning#152](https://github.com/adobe/developer.adobe.com-planning/pull/152)

### Phase 4: New Open page for all

The new Open page will be made available globally.

#### Prerequisites

- [ ] stakeholders informed in advance
- [ ] a `open-stage-prod` strain pointing to the new site

### Phase 5.1: New Product Landing and Documentation Pages

In addition to the new Open page, an entire new product page including documentation
will be made available in a restricted geo (Switzerland). See above for rationale
of limited rollout.

#### Prerequisites

- [ ] owners of the micro site / product identified
- [ ] owners of the micro site informed
- [ ] a `productx-stage-prod` and `productx-stage-test` strain pointing to the
    new sub site
- [ ] updated end-to-end tests
- [ ] Helix support for redirects
- [ ] strain-specific redirects set up

### Phase 5.2: New Product Landing and Documentation Pages For Everyone

Following phase 5.1, we will expand the rollout of the new landing and
documentation pages for everyone.

### Phase 6: Home page

Phase 5.1 and 5.2 will get repeated for the home page.

### Phase 7: More product pages

Phase 5.1 and 5.2 will get repeated for more and more existing product pages.

### Phase 8: New product pages

Until this phase, no new product pages have been introduced, giving us an easy
[fallback](#fallback) option in case of errors (see [Rollback vs. Fallback](#rollback-vs-fallback)).
From this phase on, it is necessary to provide [rollbacks](#rollback) in case of errors.

#### Prerequisites

- [ ] `hlx rollback` command for restoring an old release; *TODO* this is not
    implemented yet in helix
- [ ] `hlx deploy --cleanup` command for cleaning up outdated deployments
- [ ] updated CircleCI config to perform live-site testing after a `hlx publish`
    with automated rollback
