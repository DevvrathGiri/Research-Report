# Clinsight SEO Integration Research

## Executive Summary

Clinsight already has a strong Content Engine for research, writing, editing, humanisation, internal linking, images, social content, scheduling, publishing, and client management.

The main question is how to add deeper SEO features without rebuilding work that Clinsight already does well.

There are three products to consider:

- **DataForSEO** provides SEO data through APIs.
- **OpenSEO** turns SEO data into ready-to-use workflows.
- **Clinsight Content Engine** turns research into content and marketing activity.

These products solve different problems. DataForSEO supplies information, OpenSEO organises it into SEO workflows, and the Content Engine uses the results to help clients take action.

The recommended first step is to self-host OpenSEO, connect it through an internal SEO Adapter, and test the highest-value features. Start with backlink analysis and site audits. Add keyword research and rank tracking after the pilot proves useful.

## Purpose and Scope

This report covers the product differences, integration options, feature priorities, engineering effort, cost, risks, and rollout plan.

The goal is not to add every SEO feature at once. It is to find a sensible path to useful customer value.

## Product Comparison

### DataForSEO

DataForSEO is an SEO data supplier. Depending on the APIs selected, it can provide:

- Keyword volume, difficulty, and related terms.
- Search results and ranking positions.
- Backlink and referring-domain data.
- Technical and on-page website data.
- Competitor and domain information.
- Other search and AI visibility data where supported.

DataForSEO does not automatically provide dashboards, project management, history, alerts, client reports, or a finished user experience. Clinsight would need to build those parts around the APIs.

### OpenSEO

OpenSEO is a more complete SEO workflow layer. Relevant capabilities include:

- Keyword research.
- Rank tracking.
- Domain and competitor analysis.
- Backlink analysis.
- Technical site audits.
- AI visibility analysis.
- MCP access for agents and other clients.

OpenSEO may save development time by already handling parts of project setup, data retrieval, history, caching, and analysis. The trade-off is dependence on another application and responsibility for its deployment and maintenance.

### Clinsight Content Engine

The Content Engine already handles website research, blog creation, editing, humanisation, internal links, images, Reddit research, LinkedIn posts, scheduling, publishing, and multi-client management.

This is different from SEO intelligence. The Content Engine should use SEO information, not be replaced by an SEO tool.

```text
DataForSEO = SEO data
OpenSEO = SEO workflows
Content Engine = content and marketing execution
```

## Main Finding

OpenSEO should complement the Content Engine rather than replace it.

```text
SEO research
     -> Content planning
     -> Content production
     -> Publishing
     -> Performance tracking
     -> New SEO decisions
```

OpenSEO or DataForSEO can improve research and performance tracking. Clinsight should continue to own content planning, production, publishing, and the client-facing workflow.

Replacing the Content Engine would remove existing strengths and create unnecessary migration work. Adding a focused SEO layer is the better approach.

## Integration Options

### Hosted OpenSEO

```text
Clinsight Content Engine -> OpenSEO -> DataForSEO
```

This is the quickest option for testing the idea. It requires little infrastructure work and may provide existing SEO workflows immediately. However, Clinsight has less control over data handling, customisation, and service costs.

Hosted OpenSEO is useful for early evaluation, but it may not be suitable for sensitive data or deep product integration.

### Self-hosted OpenSEO

```text
Clinsight Content Engine -> Self-hosted OpenSEO -> DataForSEO
```

Self-hosting gives Clinsight more control over access, data, deployment, and internal integrations. It is the best option for an initial production pilot.

The trade-off is that Clinsight must manage deployments, updates, monitoring, backups, security, and incidents. DataForSEO and infrastructure costs still apply.

### Native DataForSEO Integration

```text
Clinsight Content Engine -> Clinsight SEO Module -> DataForSEO
```

A direct integration gives Clinsight full control over the user experience and data model. It also means building API wrappers, task handling, polling, retries, rate-limit management, caching, storage, dashboards, reports, scheduling, alerts, and access control.

This may be the right long-term choice for heavily used features, but it is not the fastest way to validate the wider product idea.

### DataForSEO MCP

DataForSEO MCP may be useful for experiments or agent-based workflows. It should not be treated as a complete SEO application. MCP exposes tools and data, while OpenSEO can provide projects, history, analysis, and a fuller workflow.

## Feature Priorities

| Priority | Feature                 | Reason                                                 |
| -------- | ----------------------- | ------------------------------------------------------ |
| P0       | Backlink analysis       | Supports authority checks, link-building, and reports. |
| P0       | Site audits             | Finds technical issues that may affect performance.    |
| P1       | Keyword research        | Improves topic selection and content planning.         |
| P1       | Rank tracking           | Shows progress over time and supports reporting.       |
| P2       | Local SEO               | Useful for location-based businesses.                  |
| P2       | Competitor intelligence | Helps strategy after core workflows are tested.        |

The first release should keep the outputs practical. Backlink results need filtering, history, categorisation, reporting, and links to client tasks. Audit results should connect to priorities, assigned work, and progress reports.

Keyword research should support real topic decisions rather than provide only a long list. Rank tracking should focus on historical performance rather than one-time lookups.

Clinsight already has related AI visibility functionality, so that area does not need to be an early priority.

## Suggested Architecture

```text
                         CLINSIGHT
              +--------------------------+
              |    Content Engine        |
              +------------+-------------+
                           |
                    +------+------+
                    | SEO Adapter |
                    +------+------+
                           |
                +----------+----------+
                |                     |
           OpenSEO (first)     DataForSEO (later)
                |
       Clinsight dashboards
```

The SEO Adapter should provide a small, stable interface for:

- Creating a site-audit job.
- Checking audit status.
- Retrieving audit results.
- Retrieving backlinks for a domain.
- Retrieving keyword research.
- Retrieving ranking history.
- Normalising results for storage and reports.

The Content Engine should not need to know whether a result came from OpenSEO or a direct DataForSEO call. This keeps future changes manageable.

The adapter should also handle authentication, errors, retries, logging, and workspace-level access control.

## Self-Hosting Considerations

A Docker deployment may be enough for local evaluation. A production deployment needs authentication, secret management, workspace-level access, backups, monitoring, spending limits, and an upgrade or rollback process.

The setup may require Node.js, pnpm, Cloudflare storage, and a DataForSEO account. Exact requirements should be checked against current documentation. The MCP endpoint must not be exposed publicly without proper access controls.

## Engineering Effort

These are planning estimates, not fixed commitments. Final effort depends on the current Content Engine architecture, the required interface, and testing needs.

| Workstream                             |             Rough estimate |
| -------------------------------------- | -------------------------: |
| Self-host OpenSEO, with accounts ready |              Several hours |
| Initial Content Engine integration     |       1-3 engineering days |
| First focused production pilot         |               About 1 week |
| Native backlinks and site audits       | About 5-7 engineering days |
| Broader native SEO system              |      Several weeks or more |

A self-hosted integration may require roughly 300-800 lines of custom code. A direct implementation would require more because it also includes persistence, retries, reporting, scheduling, and operational controls.

## Cost Considerations

The main costs are DataForSEO usage, Cloudflare and storage, monitoring, engineering time, and any hosted OpenSEO subscription or markup.

Actual API costs depend on the number of sites, keywords, crawl volume, SERP depth, tracking frequency, endpoints, and history refreshes. The pilot should record requests, processing time, and cost per client rather than promise a fixed monthly amount.

Vendor pricing and product terms can change, so current pricing should be checked again before procurement or budgeting.

## Risks and Mitigations

| Risk           | Concern                                       | Mitigation                                       |
| -------------- | --------------------------------------------- | ------------------------------------------------ |
| Vendor lock-in | OpenSEO may become difficult to replace.      | Keep the SEO Adapter as the internal boundary.   |
| Scope growth   | A small pilot can become a full SEO platform. | Limit the first release to backlinks and audits. |
| Maintenance    | Self-hosting creates operational work.        | Assign ownership and update procedures.          |
| Cost surprises | API usage may grow with clients.              | Add quotas, caching, monitoring, and alerts.     |
| Data exposure  | Client SEO data needs isolation.              | Use workspace controls and secure secrets.       |
| Poor adoption  | Features may not fit workflows.               | Test with a small pilot and collect feedback.    |
| Early forking  | A fork increases upgrade work.                | Integrate externally first.                      |

## Recommended Roadmap

### Phase 1: Preserve the Content Engine

Keep current content and publishing workflows. Do not rewrite or remove them as part of this work.

### Phase 2: Run a Self-hosted Pilot

Deploy OpenSEO in a controlled environment. Confirm accounts, credentials, storage, authentication, and basic monitoring.

### Phase 3: Build the SEO Adapter

Create the internal interface between Clinsight and OpenSEO. Start with MCP if it provides the required access, but keep the adapter independent from the transport method.

### Phase 4: Add P0 Workflows

Integrate backlink analysis and site audits. Connect results to client projects, dashboards, reports, and follow-up tasks where useful.

### Phase 5: Measure the Pilot

Track customer usage, API costs, response times, failure rates, and manual work saved. Use the results to decide what comes next.

### Phase 6: Add P1 Workflows

If the pilot is successful, add keyword research and rank tracking. Connect both features to content planning and reporting.

### Phase 7: Build Selectively with DataForSEO

Move a feature to a native DataForSEO integration only when it is used often enough to justify the engineering and maintenance cost.

## Final Recommendation

Clinsight should not replace the Content Engine and should not fork OpenSEO immediately.

The practical path is to self-host OpenSEO, place it behind an internal SEO Adapter, and start with backlink analysis and site audits. This provides useful SEO capability quickly while keeping future options open.

After the pilot, Clinsight can decide whether to expand OpenSEO, add keyword and ranking workflows, or build selected features directly on DataForSEO. The decision should be based on real customer demand, reliability, and measured cost.
