# Hipaa Compliance and Federated Learning

By Default, SymetryML projects are Hipaa compliant. This means that additional restrictions will be enforced for them.

### Safe Harbor

Please note that it is assumed that the data is Safe Harbor Compliant. In the absence of expert determination that no health information about individual patient can be identified in your data, the Safe Harbor method must be used to make sure that no patient personal health information can be leaked. Please consult [https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html](https://www.hhs.gov/hipaa/for-professionals/privacy/special-topics/de-identification/index.html) if your organization needs more detail of the Safe Harbor method.

## The following is true if SMPC is not Enabled

Please consult the [section on SMPC](about-federated-learning.md#secure-multi-party-computation-mode) for further details on enabling Secure Multi-Party Computation (SMPC) for your federation.

* When adding a data source to a Hipaa compliant project, one must specify which column(s) uniquely identify a patient. This means that at least one of column of the data source must have an attribute type equals to `'P'` which stands for `TYPE_PATIENT_ID`.
* When a federation node is syncing with other nodes in the federation it will need to have at least 25 unique patients in the DEM for the sync to be sent.
* Additional Statistical tests will be performed on each feature between each sync to ensure that no information is leaked. If one of these tests do not pass a node will not send their sync. These tests are protecting against information that a malicious user could possibly compute by comparing the current synchronization DEM with the previous synchronization DEM for a given peer.

### Additional Statistical Tests

This section describes the univariate statistical test that are performed on each attribute of your dataset when Hipaa compliance is enabled in a federated learning project.

#### T-Test

Suppose $$x$$ is one of the attributes. We have a starting sample 1 and a desired update, sample 2.&#x20;

The following $$t$$-test is used

$$
t = \frac{\mu_1 - \mu_2}{\sqrt{\frac{S_1^2}{n_1} + \frac{S_2^2}{n_2}}},
$$

where the sample variance $$S_{j}$$ $$(j = 1 \text{ or } 2)$$ is defined as

$$
S_{j}= \frac{1}{n_j}\sum_{i=1}^{n_{j}}(x_{i} - \mu_j)^{2}
$$

and the sample mean $$\mu_j$$ is defined by

$$
\mu_j = \frac{1}{n_j} \sum_{i=1}^{n_{j}} x_{i}.
$$

The test will insist that $$P[T > |t|] < \epsilon$$ where $$T$$ is a $$t$$-distributed variable whose degrees of freedom will be estimated by

$$
df = \frac{\left( \frac{S_1^2}{n_1} + \frac{S_2^2}{n_2} \right)^2}{ \frac{\left(\frac{S_1^2}{n_1}\right)^2}{n_1 - 1} + \frac{\left(\frac{S_1^2}{n_2}\right)^2}{n_2 - 1} }.
$$

Where one if the standard deviation vanishes, for example when $$S_2 = 0$$, then a one-sample $$t$$-test is performed - and then evaluated against a $$t$$-distribution with $$n_{1}$$ degrees of freedom:

$$
t = \frac{\mu_1 - \mu_2}{\frac{S_1}{\sqrt{n_1}}}.
$$

The $$t$$-test will ensure that we accept the NULL-hypothesis that the existing population and the DEM update were drawn from populations of the same mean at 95% confidence. Where both variances vanish, the update should only be accepted when $$\mu_1 = \mu_2$$.

The DEM of both sample 1 and 2 are all is needed to extract the necessary values for $$S_j$$, $$\mu_j$$ and $$n_j$$ regarding a sync.

#### Kolmogorov-Smirnov Test

We also carry out a Kolmogorov-Smirnov test to compare the distribution of the update to the previous distribution. This is a non-parametric test to compare two distributions. The idea here is that we would like any update “to represent the overall distribution and not special features of the individuals in the update.”

Suppose again $$x$$ is one of the attributes, but now assume sorted ascending values $$x_1, . . . , x_n$$. An update adds $$m$$ new records, also sorted in an ascending way, $$x^{′}_1, . . . , x^{′}_m$$. Typically $$m ≪ n$$. We accept the update if “it looks like a random sample from the existing distribution”, but reject it otherwise as it might then convey information specific to the individuals in the sample. The statistics to achieve that are covered in the Subsections below.

We define:

$$
\Phi(t) = \max \biggl\{ \frac{i}{n} \bigg| 1 \leq i \leq n, x_i \leq t\biggl\} = \frac{1}{n} \sum_{i=1}^{n} 1_{(- \infty, t]} (x_i)
$$

$$
\Phi'(t) = \max \biggl\{ \frac{i}{m} \bigg| 1 \leq i \leq m, x'_i \leq t\biggl\} = \frac{1}{n} \sum_{i=1}^{n} 1_{(- \infty, t]} (x'_i).
$$

We then define:

$$
D = \mathsf{sup}_t \Big|\Phi(t) - \Phi'(t) \Big|
$$

and we require that

$$
D_{n,m} < K(\alpha, n, m) = c(\alpha) \sqrt{\frac{n +m}{nm}}
$$

with $$c = 1.358$$ for a 95% confidence level, as per the formula

$$
c(\alpha) = \sqrt{-\ln(\frac{\alpha}{2} ) \cdot \frac{1}{2}}
$$

The KS-test of the DEM update against the existing population must then be smaller than $$K(0.05, n. m)$$.

#### Integral Test

Given that $$m \ll n$$ (the update to a DEM will typically contain far fewer records than the existing data it), we cannot expect the KS-statistic to be zero or arbitrarily close to zero, because a step function with $$m < n$$ steps cannot generally perfectly approximate a step function with $$n$$ steps.

The integral of the difference can in principle become arbitrarily small:

$$
I = \frac{1}{\sqrt{\left(x_n - x_1\right)\left(x^{'}_m - x^{'}_1\right)}}\int_{-\infty}^{\infty}\left(\Phi(t) - \Phi^{'}(t)\right)dt
$$

This is only defined when both variables are non-constant  ($$x_n > x_1$$, $$x^{′}_m > x^{′}_1$$)

This test will pass if the integral $$I$$ satisfies one of the following conditions depending on the value of $$m$$:

$$
\begin{array}{rcl} \mbox{if } m \le 10 & \Longrightarrow & I < 0.2 \\ \mbox{if } 11 \le m \le 25 & \Longrightarrow & I < 0.1 \\ \mbox{if } m > 25 & \Longrightarrow & I < 0.05 \end{array}
$$

The distribution information for the attributes provided along the DEM for $$x$$ and $$x^{'}$$ enable these calculations for the Kolmogorov-Smirnov and Integral tests to be performed by SymetryML.

## When SMPC is Not Enabled

Before the SMPC protocol is run to exchange all the peer's DEM, a verification will be made to ensure that the resulting DEM would contain at least 25 patients. No additional statistical test will be run.

## When Hipaa Compliance is not Needed

If your project does not require to be Hipaa compliance, please specify `fed_enforce_hipaa_compliance=false` as part of the **Federation Key Map Value** passed in the message body when using the REST API to create a federation. Please consult either '[Create AWS Backed Federation](federated-learning-api/federated-learning-with-aws-1.md#create-aws-backed-federation)' or '[Create NATS Backed Federation](federated-learning-api/federated-learning-with-nats-1.md#create-nats-backed-federation)' sections for details.
