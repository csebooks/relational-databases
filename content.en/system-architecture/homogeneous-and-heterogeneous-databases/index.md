---
title: Homogeneous and Heterogeneous Databases
weight: 18
---

Homogeneous and Heterogeneous Databases## Homogeneous and Heterogeneous Databases

In a **homogeneous distributed database** system, all sites have identical database- management system software, are aware of one another, and agree to cooperate in processing users’ requests. In such a system, local sites surrender a portion of their autonomy in terms of their right to change schemas or database-management

_branch_(_branch_name_, _branch_city, assets_) 
_account_ (_account_number_, _branch_name, balance_)
_depositor_ (_customer_name_, _account_number_)

**Figure 19.1** Banking database.

system software. That software must also cooperate with other sites in exchanging information about transactions, to make transaction processing possible across multiple sites.

In contrast, in a **heterogeneous distributed database**, different sites may use different schemas, and different database-management system software. The sites may not be aware of one another, and they may provide only limited facilities for cooperation in transaction processing. The differences in schemas are often a major problem for query processing, while the divergence in software becomes a hindrance for processing transactions that access multiple sites.

In this chapter, we concentrate on homogeneous distributed databases. However, in Section 19.8 we briefly discuss issues in heterogeneous distributed database systems.

