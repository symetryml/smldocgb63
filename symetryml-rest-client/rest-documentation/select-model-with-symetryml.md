# Select Model with SymetryML

## Select Model introduction

SymetryML **Select Model** is a feature selection functionality. It allows to automatically select the best features for a given model algorithm and it leverages SymetryML unique capabilities to build different predictive model quickly. The functionality builds various model each with different input attributes using a predefined heuristic. It then computes a score for them using out of sample data and will retain the best one. The following table describes the available heuristics:

### Select Heuristic

| Select heuristic             |                                                                                                                                                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Name                         | Description                                                                                                                                                                                                                           |
| **Forward Backward**         | A heuristic that does the following: 1. Iteratively add as many features as possible while keeping the best model 2. Iteratively remove as many feature as possible while keeping the best model 3. repeat a specific number of time. |
| **Brute Force**              | Brute force will try all possible combinations of the input attributes. It should not be used if you have more than 17-18 attributes.                                                                                                 |
| **Max Number of Iterations** | Randomly create a model by trying a specific number of random number of permutations of the features.                                                                                                                                 |
| **Max. Number of Seconds**   | Randomly create a model by trying a random number of permutations of the features for a maximum number of seconds.                                                                                                                    |
| **Simple**                   | The simple heuristic starts with one feature and then incrementally adds one additional feature until it tries all the features. It then keeps track of the best model.                                                               |

### Selector Types

| Parameter                     | Description                                                                                                                                                                                                                 |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **selector\_type\_fw\_bw**    | Forward / Backward heuristic. Number of iteration is by default 5. It can be controlled with the `selector_max_iterations` parameters.                                                                                      |
| **selector\_type\_simple**    | Simple heuristic                                                                                                                                                                                                            |
| **selector\_type\_brute**     | Brute force selector.                                                                                                                                                                                                       |
| **selector\_type\_iteration** | A Selector that will either try a specific number of random combination or will try for a specific number of seconds. `selector_max_iterations` or `selector_max_seconds` must also be specified with this type of selector |
| **selector\_type\_genetic** | **(Experimental)** Genetic Algorithm feature selector. Uses evolutionary optimization to find optimal feature subsets. See [Genetic Algorithm Selector](#genetic-algorithm-selector-experimental) section. |
| **selector\_type\_bayesian** | **(Experimental)** Bayesian Optimization feature selector. Uses probabilistic modeling to efficiently search the feature space. See [Bayesian Optimization Selector](#bayesian-optimization-selector-experimental) section. |

### Selector Grid

Elastic Net model has 2 hyper parameters that can be optimized _eta_ and _lambda_. The auto-select algorithm will try various combinations of these parameters using a grid search. The size of this grid can be controlled via the `autoselect_grid_type` extra parameter in the [MLContext](appendix-a-json-data-structure-schema.md#mlcontext-json) request body. Please see this [section](auto-select-with-symetryml.md#sample-request-response-regression-1) for such an example.

| Parameter                          | Description                                                                                                         |
| ---------------------------------- | ------------------------------------------------------------------------------------------------------------------- |
| **autoselect\_grid\_type\_tiny**   | eta \[0, 0.5, 1.0] x lambda \[1e-3, 1e-2, 0.1]                                                                      |
| **autoselect\_grid\_type\_small**  | eta \[0, 0.5, 1.0] x lambda \[1e-3, 1e-2, 0.1, 1]                                                                   |
| **autoselect\_grid\_type\_normal** | eta \[0, 0.3333, 0.6666, 1.0] x lambda \[1e-4, 1e-3, 1e-2, 0.1, 1, 10]                                              |
| **autoselect\_grid\_type\_large**  | eta \[0, 0.2, 0.4, 0.6, 0.8, 1.0] x lambda \[1e-9, 1e-8, 1e-7, 1e-6, 1e-5, 1e-4, 1e-3, 1e-2, 0.1, 1, 10, 100, 1000] |

## Select Model Rest API

Allows to invoke the _select model_ functionality by specifying an external data source id as the out of sample data to use for model assessment.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/selectModel/{dsid} [body=MLContext]
```

### Query Parameters

| Parameter   | Required / Optional | Description                    |
| ----------- | ------------------- | ------------------------------ |
| **modelid** | Required            | ID to assign to the new model. |
| **algo**    | Required            | Algorithm to fit               |

### MLContext Build Parameters

| Parameter                  | Required / Optional | Type    | Description                                                                                                                                                                                                       |
| -------------------------- | ------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rnd\_seed**              | Optional            | Integer | Set the seed of the randomizer                                                                                                                                                                                    |
| **selector\_type**         | Optional            | String  | Default is **selector\_type\_fw\_bw**. Please see [Selector Heuristic](select-model-with-symetryml.md#select-heuristic) and [Selector Types](select-model-with-symetryml.md#selector-types) sections for details. |
| **autoselect\_grid\_type** | Optional            | String  | Default is **autoselect\_grid\_type\_tiny**. Please see [Selector Grid Table](select-model-with-symetryml.md#selector-grid) for details.                                                                          |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| 202              | OK                  | Job accepted.                                                                                                                               |
| 400              | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### Sample Request Response Classifier

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel/Iris_rtlm.csv?algo=lda&modelid=lda1

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
    "targetAttributeNames":["Iris_virginica"],
    "extraParameters":{"rnd_seed":"1"}
}
```

### Sample Request Response Regression

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel/Iris_rtlm.csv?algo=mlr&modelid=mlr1

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
    "targetAttributeNames":["sepal_length"],
    "extraParameters":{"rnd_seed":"1"}}

}
```

## Select Model Dataframe Rest API

Allows to invoke the _select model_ functionality by using a [DataFrame](appendix-a-json-data-structure-schema.md#dataframe-json) passed in the request body as the out of sample data to be used for models assessment.

### URL

```
POST /symetry/rest/{cid}/projects/{pid}/selectModel [body=Map{"dataframe"=DataFrame, "mlcontext"=MLContext}]
```

### Query Parameters

| Parameter   | Required / Optional | Description                    |
| ----------- | ------------------- | ------------------------------ |
| **modelid** | Required            | ID to assign to the new model. |
| **algo**    | Required            | Algorithm to fit               |

### MLContext Build Parameters

| Parameter                  | Required / Optional | Type    | Description                                                                                                                                                                                                       |
| -------------------------- | ------------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **rnd\_seed**              | Optional            | Integer | Set the seed of the randomizer                                                                                                                                                                                    |
| **selector\_type**         | Optional            | String  | Default is **selector\_type\_fw\_bw**. Please see [Selector Heuristic](select-model-with-symetryml.md#select-heuristic) and [Selector Types](select-model-with-symetryml.md#selector-types) sections for details. |
| **autoselect\_grid\_type** | Optional            | String  | Default is **autoselect\_grid\_type\_tiny**. Please see [Selector Grid Table](select-model-with-symetryml.md#selector-grid) for details.                                                                          |

### HTTP Responses

| HTTP Status Code | HTTP Status Message | Description                                                                                                                                 |
| ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| **202**          | OK                  | Job accepted.                                                                                                                               |
| **400**          | BAD REQUEST         | Unknown SymetryML project. `{"statusCode":"BAD_REQUEST","statusString":" + Cannot Find SYMETRYML id[r2] for Customer id [c1]","values":{}}` |

### Sample Request Response Classifier

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel?algo=lda&modelid=lda1

Body:
{
    "mlcontext" : {
        "targets":[],
        "inputAttributes":[],
        "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "targetAttributeNames":["Iris_virginica"],
        "extraParameters":{"rnd_seed":"1"}}
    "dataframe" : {
        "attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]
        "data":[
            ["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"]
            (...)
            ["7.2","3.6","6.1","2.5","0","1","0","1","0","1","0","1","0","0","1"]
        ],

    }

}
```

### Sample Request Response Regression

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel?algo=mlr&modelid=mlr1

Body:
{
    "mlcontext" : {
        "targets":[],
        "inputAttributes":[],
        "inputAttributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "targetAttributeNames":["sepal_length"],
        "extraParameters":{"rnd_seed":"1"}}
    "dataframe" : {
        "attributeNames":["sepal_length","sepal_width","petal_length","petal_width","sepal_lengt_b1","sepal_lengt_b2","sepal_width_b1","sepal_width_b2","petal_length_b1","petal_length_b2","petal_width_b1","petal_width_b2","Iris_setosa","Iris_versicolor","Iris_virginica"],
        "attributeTypes":["C","C","C","C","B","B","B","B","B","B","B","B","B","B","B"]
        "data":[
            ["4.3","3","1.1","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.8","3","1.4","0.1","1","0","0","1","1","0","1","0","1","0","0"],
            ["4.9","3.1","1.5","0.1","1","0","0","1","1","0","1","0","1","0","0"]
            (...)
            ["7.2","3.6","6.1","2.5","0","1","0","1","0","1","0","1","0","0","1"]
        ],

    }
}
```

---

## Genetic Algorithm Selector (Experimental)

The Genetic Algorithm selector uses evolutionary optimization to find optimal feature subsets. It evolves a population of candidate feature sets over multiple generations, using selection, crossover, and mutation operations to discover high-performing feature combinations.

### When to Use

- When you have a large number of features and want to explore the feature space more thoroughly than forward/backward selection
- When feature interactions are important and simple greedy approaches may miss optimal combinations
- When you can afford more computation time for potentially better results

### Genetic Algorithm Parameters

| Parameter | Type | Default | Description |
| --------- | ---- | ------- | ----------- |
| **genetic\_population\_size** | Integer | 50 | Number of candidate feature sets in each generation |
| **genetic\_num\_generations** | Integer | 100 | Maximum number of generations to evolve |
| **genetic\_mutation\_rate** | Double | 0.05 | Probability of flipping each feature (gene) during mutation |
| **genetic\_crossover\_rate** | Double | 0.8 | Probability of performing crossover between two parents |
| **genetic\_elite\_count** | Integer | 2 | Number of top-performing individuals preserved unchanged each generation |
| **genetic\_tournament\_size** | Integer | 3 | Number of individuals competing in tournament selection |
| **genetic\_initial\_feature\_prob** | Double | 0.1 | Probability that each feature is included in initial random population |
| **genetic\_min\_features** | Integer | 1 | Minimum number of features allowed in any individual |
| **genetic\_max\_features** | Integer | unlimited | Maximum number of features allowed in any individual |
| **genetic\_parallel\_threads** | Integer | 4 | Number of parallel threads for model evaluation |
| **genetic\_stagnation\_limit** | Integer | 20 | Number of generations without improvement before early stopping |

### Sample Request

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel/test_data.csv?algo=lda&modelid=lda_genetic

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":["attr1","attr2","attr3","attr4","attr5"],
    "targetAttributeNames":["target"],
    "extraParameters":{
        "selector_type":"selector_type_genetic",
        "genetic_population_size":"100",
        "genetic_num_generations":"50",
        "genetic_stagnation_limit":"15"
    }
}
```

---

## Bayesian Optimization Selector (Experimental)

The Bayesian Optimization selector uses probabilistic modeling to efficiently search the feature space. It builds a surrogate model of the objective function and uses an acquisition function to balance exploration and exploitation when selecting which feature combinations to evaluate.

### When to Use

- When model evaluation is expensive and you want to minimize the number of evaluations
- When you want a more sample-efficient search compared to random or genetic approaches
- When the feature space is large but you suspect good solutions exist in specific regions

### Bayesian Optimization Parameters

| Parameter | Type | Default | Description |
| --------- | ---- | ------- | ----------- |
| **bayesian\_num\_iterations** | Integer | 100 | Total number of optimization iterations |
| **bayesian\_initial\_random** | Integer | 20 | Number of random samples before starting Bayesian optimization |
| **bayesian\_exploration\_weight** | Double | 0.1 | Exploration weight for UCB (Upper Confidence Bound) acquisition function |
| **bayesian\_num\_candidates** | Integer | 100 | Number of candidate feature sets evaluated per iteration |
| **bayesian\_local\_search\_steps** | Integer | 10 | Number of local search steps for solution refinement |
| **bayesian\_embedding\_dim** | Integer | 50 | Dimension for random embedding when dealing with high-dimensional feature spaces |
| **bayesian\_top\_k\_memory** | Integer | 200 | Number of top observations kept in memory for surrogate model |
| **bayesian\_stagnation\_limit** | Integer | 30 | Number of iterations without improvement before early stopping |

### Sample Request

```
Request:
POST url="/symetry/rest/c1/projects/p1/selectModel/test_data.csv?algo=mlr&modelid=mlr_bayesian

Body:
{
    "targets":[],
    "inputAttributes":[],
    "inputAttributeNames":["attr1","attr2","attr3","attr4","attr5"],
    "targetAttributeNames":["target"],
    "extraParameters":{
        "selector_type":"selector_type_bayesian",
        "bayesian_num_iterations":"50",
        "bayesian_initial_random":"10",
        "bayesian_stagnation_limit":"20"
    }
}
```
