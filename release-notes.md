# SymetryML Release Notes

---

## Version 6.2

### New Features

#### SSO Authentication Support
SymetryML now supports Single Sign-On (SSO) authentication via OIDC (OpenID Connect) and LDAP. After SSO authentication, SymetryML credentials (username and secret key) are returned for API access. SSO can also auto-create SymetryML user accounts based on the SSO profile.

#### Server Logs API
A new REST endpoint allows retrieval of the last N lines from the server log file. The number of lines returned is controlled by the `SML_LOG_TAIL_SIZE` configuration parameter.

```
GET /symetry/rest/{cid}/logs
```

### Infrastructure Changes

- **Jakarta EE Migration:** Migrated from `javax.ws.rs` to `jakarta.ws.rs` (JDK 11 support)
- All existing REST endpoints maintain full backward compatibility

---

## Version 6.3

### New REST API Endpoints

#### LearnPredict
Combines learn and predict operations in a single call, enabling real-time learn-then-predict workflows without separate requests.

```
POST /symetry/rest/{cid}/projects/{pid}/learnpredict/{modelid}
```

#### Performance Estimation
Estimates supervised model performance on production data when ground truth labels are not yet available. Model-agnostic and label-free, it supports any binary classification model and estimates confusion matrix-based metrics (ROC curve, precision-recall curve, precision, recall, accuracy, F1 score, specificity). Based on the Probabilistic Adaptive Performance Estimation (PAPE) framework with a SymetryML-optimized single-pass implementation for streaming environments.

```
POST /symetry/rest/{cid}/projects/performanceEstimator
```

#### Data Drift Detection
Detects data drift between a baseline project and an analysis project using multiple drift metrics: PCA reconstruction error, global drift, marginal statistical, and marginal non-statistical methods. Integrates with Rolling Window projects for continuous drift monitoring.

```
POST /symetry/rest/{cid}/projects/dataDrift
```

#### EVT Wrapper Management
Creates or deletes Extreme Value Theory (EVT) wrappers on any model for dynamic anomaly thresholding. Instead of static thresholds, EVT wrappers track prediction outputs and use Extreme Value Theory to dynamically flag anomalies.

```
POST /symetry/rest/{cid}/projects/{pid}/{modelid}/evtwrapper
DELETE /symetry/rest/{cid}/projects/{pid}/{modelid}/evtwrapper
```

#### Conditional Density Estimates
Computes conditional density estimates (histograms) for pairs of attributes, useful for bivariate analysis with conditional distributions. Supports federated projects via the `useLocal` parameter.

```
POST /symetry/rest/{cid}/projects/{pid}/conditionalDensityEstimate
```

### Survival Analysis

#### Cumulative Incidence Function (CIF)
New survival analysis model for competing risks. Unlike standard survival methods that can overestimate event probabilities by treating competing events as censored, CIF properly accounts for the fact that experiencing one event precludes another.

- **Get Total Events** -- `POST /{cid}/projects/{pid}/cif/NtotEvents`
- **Predict At Risk** -- `POST /{cid}/projects/{pid}/cif/predictAtRiskCIF`
- **Predict Event** -- `POST /{cid}/projects/{pid}/cif/predictEvent`

CIF project configuration parameters: `cif_time_column`, `cif_event_column`, `cif_group_column`, `cif_event_of_interest`, and `sml_surv_type` (set to `cif`).

#### Kaplan-Meier REST Endpoints
New REST endpoints for querying Kaplan-Meier survival models, including survival data frame retrieval and survival predictions with confidence intervals.

- **Get Survival DataFrames** -- `GET /{cid}/projects/{pid}/km/getSurvivalFrame`
- **Kaplan-Meier Predict** -- `POST /{cid}/projects/{pid}/km/kaplanmeier`

### Federated Learning

#### Contract Management
New endpoints for managing data sharing contracts on federated projects:

- **Add Contract** -- `POST /{cid}/fedml/{pid}/addContract`
- **Validate Contract** -- `POST /{cid}/fedml/{pid}/contractValidate`

#### Federation Invitation
New alias endpoint for getting encrypted federation invitations:

```
POST /symetry/rest/{cid}/fedml/{pid}/getInvitation
```

The previous `getEncrypted` path is preserved for backward compatibility.

### Exploration Enhancements

- **Kurtosis:** New univariate statistic (`kurtosis`) available in exploration results.
- **Enhanced Bivariate Statistics:** New metrics including conditional sum (`condSum`), conditional standard deviation (`condStddev`), and 95% confidence interval bounds (`biCi95Down`, `biCi95Up`).
- **Bivariate Conditional Flags:** New parameters `need_bi_conditional`, `need_bi_conditional_mmm`, and `need_bi_conditional_ci95` to request conditional statistics in bivariate exploration.
- **PCA Max Dimension:** PCA results are now limited to 100 dimensions maximum by default. This value is configurable.
- **Explore Endpoint:** New `returnProjectInfo` query parameter to control whether project info is included in the response.
- **Density Estimates:** New `useLocal` parameter for federation support; improved threading.

### Data Sources

#### Snowflake Data Source
New Snowflake data source type (`snow`) with support for:
- Account, user, role, and private key authentication
- Database, schema, and table configuration
- Snowpark Container Services (CP3O) compute pool configuration
- Job warehouse settings and data frame size limits

#### NATS Data Source
New NATS data source type (`nats`) with support for three data formats: Protobuf, JSON, and CSV. Protobuf format requires `protobuf.schema` and `protobuf.msg.type.name` parameters.

#### Spark 4.1.0
Spark support updated to version 4.1.0, required due to changes in the underlying JDK and Jetty server. Only Spark 4.1.0 is supported in this version.

### Model Selection and AutoML

#### Genetic Algorithm Feature Selector (Experimental)
New evolutionary optimization approach for feature selection. Evolves a population of candidate feature sets over multiple generations using selection, crossover, and mutation to discover high-performing feature combinations. Use `selector_type_genetic` as the selector type.

#### Bayesian Optimization Feature Selector (Experimental)
New probabilistic modeling approach for efficient feature space search. Builds a surrogate model and uses an Upper Confidence Bound acquisition function to balance exploration and exploitation. Use `selector_type_bayesian` as the selector type.

#### MRMR Filter
New Minimum Redundancy Maximum Relevance filter (`use_mrmr_filter`) for feature pre-filtering during model selection.

#### Early Detection Assessment Metric
New `early_detection` assessment metric for binary classification models. Rewards models that return a positive prediction earlier in a sequence of observations. Designed for anomaly or malfunction detection on equipment where earlier detection yields a better score.

#### Feature Interaction Limit
New `automl_feature_interaction_max_number` parameter to control the maximum number of feature interactions during AutoML.

### Rolling Window Projects
New project configuration for retaining statistics from only the most recent N rows of learned data:
- `sml_project_rolling_window_size` -- specifies the rolling window length
- `sml_project_rolling_window_use_embedded` -- creates an embedded project inside the main project for the rolling window
- The embedded project can be referenced in the Data Drift API using `rolling_window_embedded_project` as the analysis project name

### Infrastructure Changes

- **Logging Framework:** Migrated from Log4j 1.x to Log4j 2.x
- **Jetty Upgrade:** Updated to Jetty 12.x (Jakarta EE)
- **Explore Endpoint:** Updated with `returnProjectInfo` parameter
- **Density Estimates:** Updated with federation support and improved threading
