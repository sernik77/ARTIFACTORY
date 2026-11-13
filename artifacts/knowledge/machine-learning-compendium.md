---
title: Machine Learning Compendium
kind: knowledge
status: stable
summary: "Reference compendium of ML paradigms, algorithms and concepts (formula images from the source were dropped on import)."
tags: [machine-learning, compendium]
created: 2026-03-26
updated: 2026-03-26
origin: "https://docs.google.com/document/d/1z5YZ191NEuSQknFZYgd5-J-O4br2shtCLARxT8Da2J4"
---

# Machine Learning Compendium

## **Paradigms**

Machine learning paradigms dictate the overarching frameworks and operational philosophies governing how data is presented to a computational system and how the learning objective is fundamentally structured.

## **Supervised learning**

Supervised learning operates by training a mathematical model to approximate a mapping function  using a dataset comprised of paired input features and explicit, labeled "ground truth" target outputs. The algorithm relies on empirical risk minimization, systematically updating parameters via optimization algorithms like stochastic gradient descent to continuously minimize a predefined loss function measuring the deviation between predictions and actual labels.1 Generalization, the ability to seamlessly translate high performance on training data to accurate predictions on completely novel, unseen data, is the core empirical objective.1 **Notable Insights & Ciekawostki** Most real-world industrial deployments currently rely on supervised learning because of its predictable convergence bounds, though it remains inherently bottlenecked by the extreme financial and temporal costs associated with manual human data annotation.1 !([https://upload.wikimedia.org/wikipedia/commons/e/ec/Supervised\_machine\_learning\_in\_a\_nutshell.svg](https://upload.wikimedia.org/wikipedia/commons/e/ec/Supervised_machine_learning_in_a_nutshell.svg))

## **Unsupervised learning**

Unsupervised learning algorithms extract hidden topological structures, probabilistic distributions, and localized patterns from raw datasets that completely lack explicit human-provided labels. This paradigm fundamentally relies on mechanisms such as clustering to segregate data into morphologically similar groups, and dimensionality reduction to compress the feature space while mathematically preserving essential variance and intrinsic geometric properties.1 **Notable Insights & Ciekawostki** It acts as the vanguard of exploratory data analysis, enabling computational systems to autonomously identify anomalous deviations or novel topological features without any preconceived human bias regarding what constitutes a standard class.2

## **Semi-supervised learning**

Semi-supervised learning bridges the operational gap between supervised and unsupervised methodologies by leveraging a sparse subset of labeled data alongside a massive, unannotated corpus to drastically improve learning accuracy. The approach functions on the underlying manifold assumption and cluster assumption, propagating available labels geometrically across the continuous density of the unlabeled data space via techniques like pseudo-labeling.3 **Notable Insights & Ciekawostki** This paradigm provides immense economic utility in highly specialized fields, such as genomic sequencing or radiographic imaging, where raw data is infinitely abundant but extracting expert-level annotations is prohibitively expensive and time-consuming.3 !([https://upload.wikimedia.org/wikipedia/commons/c/c2/Semi-supervised\_learning\_example.png](https://upload.wikimedia.org/wikipedia/commons/c/c2/Semi-supervised_learning_example.png))

## **Self-supervised learning**

Self-supervised learning (SSL) dynamically transforms an inherently unsupervised problem into a supervised framework by algorithmically generating synthetic labels directly from the structural relationships within the data itself.3 Typical mechanisms involve deliberately masking portions of an input sequence—such as occluding patches of a digital image or dropping tokens from a sentence—and forcing the neural architecture to mathematically predict the missing components, thereby learning deep, generalizable semantic representations.3 **Notable Insights & Ciekawostki** SSL is the foundational pre-training methodology powering the explosive rise of Large Language Models (LLMs), allowing architectures with hundreds of billions of parameters to ingest the entirety of the internet without a single manual human label.3 !([https://upload.wikimedia.org/wikipedia/commons/1/1b/Self-supervised\_learning.png](https://upload.wikimedia.org/wikipedia/commons/1/1b/Self-supervised_learning.png))

## **Reinforcement learning**

Reinforcement learning (RL) constructs a behavioral paradigm wherein an autonomous algorithmic agent learns to execute sequential decisions by continuously interacting with a dynamic environment to strictly maximize a cumulative numerical reward.2 Governed by the mathematics of Markov Decision Processes (MDP), the agent continuously refines its policy  or value function  using recursive Bellman equations, navigating the delicate trade-off between exploiting known rewarding actions and exploring uncharted state spaces.4 **Notable Insights & Ciekawostki** The agent operates via an iterative action-reward feedback loop where environmental state transitions provide immediate, localized feedback, allowing the model to autonomously deduce highly complex strategies that entirely elude human game theorists.3 !([https://upload.wikimedia.org/wikipedia/commons/1/1b/Reinforcement\_learning\_diagram.svg](https://upload.wikimedia.org/wikipedia/commons/1/1b/Reinforcement_learning_diagram.svg))

## **Meta-learning**

Meta-learning, commonly defined as "learning to learn," fundamentally optimizes the internal learning process itself, granting artificial intelligence models the capacity to adapt instantaneously to completely novel tasks using only a handful of examples.5 The algorithms are rigorously trained on the dynamic predictions, gradient updates, and historical metadata of multiple base-learner algorithms across a vast distribution of tasks, often employing bi-level optimization where an inner loop adapts to a specific task while an outer loop optimizes the foundational parameter initialization.5 **Notable Insights & Ciekawostki** This paradigm directly mimics human meta-cognition, pushing the boundaries of artificial intelligence toward true few-shot learning capabilities by treating the learning algorithm not as a static rule, but as a mathematically trainable parameter.6

## **Online learning**

Online machine learning operates by incrementally updating the model's internal parameters as a continuous, unbounded stream of sequential data arrives, rather than training on a static, finite dataset. This highly dynamic paradigm executes stochastic gradient updates on single instances or micro-batches, requiring rigorous algorithmic mechanisms to mitigate catastrophic forgetting and adapt continuously to structural concept drift within the data stream.

**Notable Insights & Ciekawostki**

It is an indispensable architectural requirement for high-frequency algorithmic trading systems and real-time social media recommendation engines, where the underlying statistical distribution of user behavior exhibits relentless, daily mutations.

## **Batch learning**

Batch learning, or offline learning, processes the entirety of the available historical dataset in a single, massive computational phase to statically train a predictive model. The underlying parameters are globally optimized over a fixed statistical distribution; if the data distribution fundamentally shifts, the entire architecture must be retrained from scratch, ensuring high mathematical stability but exceptionally low temporal adaptability.

**Notable Insights & Ciekawostki**

While computationally monolithic and expensive during the training lifecycle, batch inference is heavily optimized for modern GPU tensor cores, allowing for massive, stable throughput via parallelized matrix multiplications.

!([https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Batch\_learning.png/800px-Batch\_learning.png](https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Batch_learning.png/800px-Batch_learning.png))

## **Curriculum learning**

Curriculum learning mandates the organization of training data into a structured, meaningful sequence, initializing the optimization process with simple, low-variance examples and progressively escalating the mathematical complexity.8 This pacing strategy dramatically smoothens the non-convex optimization landscape during early epochs, preventing the network from converging into poor local minima when immediately confronted with chaotic, high-entropy data manifolds.9 **Notable Insights & Ciekawostki** This methodology perfectly mirrors the structured pedagogical progression of human formal education, acting as an advanced sample-selection mechanism that drastically improves global convergence speed in highly complex deep learning applications.8

## **Rule-based learning**

Rule-based machine learning bypasses continuous, gradient-based neural weights, instead extracting, evolving, or curating a discrete set of relational IF-THEN logical expressions to map captured knowledge.12 Algorithms within this paradigm, such as RIPPER, dynamically grow rule branches to isolate positive examples and ruthlessly prune them to reduce error, generating predictive models that are fundamentally governed by strict propositional logic rather than abstract geometric manifolds.12 **Notable Insights & Ciekawostki** These models offer absolute, perfect interpretability and gracefully bypass the opaque "black-box" nature of modern deep learning, though they consistently struggle when deployed against high-dimensional, continuous unstructured data like audio or imagery.13 !([https://upload.wikimedia.org/wikipedia/commons/thumb/8/8e/Rule-based\_machine\_learning.png/800px-Rule-based\_machine\_learning.png](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8e/Rule-based_machine_learning.png/800px-Rule-based_machine_learning.png))

## **Neuro-symbolic AI**

Neuro-symbolic AI hybridizes the raw, statistical pattern-recognition power of deep neural networks with the rigorous, deductive logic and explicit cognitive reasoning of classical symbolic AI.14 It ingests noisy, unstructured real-world data using perceptual neural layers to extract semantic symbols, which are then passed directly into a symbolic reasoning engine to execute formal deductive logic, enforce strict operational constraints, and guarantee interpretable decision pathways.15 **Notable Insights & Ciekawostki** This paradigm is a direct computational implementation of Daniel Kahneman's "System 1 and System 2" cognitive theory, effectively synthesizing fast, intuitive sub-symbolic perception with slow, deliberate, and robust logical reasoning.14

## **Neuromorphic engineering**

Neuromorphic engineering discards traditional digital logic, instead designing physical hardware and sub-threshold silicon architectures that structurally and electrically mimic the asynchronous dynamics of biological nervous systems.17 It heavily utilizes Spiking Neural Networks (SNNs) intertwined with novel non-volatile analog memory technologies, such as Memtransistors and Electrochemical RAM (ECRAM), to perform massive parallel compute-in-memory, utterly bypassing the severe latency of the Von Neumann bottleneck.17 **Notable Insights & Ciekawostki** Oxygen vacancy ECRAM devices provide inherently symmetric and deterministic conductance programming, allowing deep learning matrix-multiply operations to execute analogically at a fraction of the energy cost required by traditional digital processors.18

## **Quantum machine learning**

Quantum machine learning (QML) operates at the profound intersection of quantum physics and statistical learning theory, leveraging superconducting qubits and entangled quantum logic gates to manipulate data representations inside exponentially vast Hilbert spaces.20 By actively exploiting superposition and quantum interference, hybrid quantum-classical algorithms navigate complex, non-convex loss landscapes, theoretically providing massive polynomial or exponential speedups for highly specific classes of classification and simulation tasks.20 **Notable Insights & Ciekawostki** QML introduces the revolutionary concept of "Quantum Kernels," effectively mapping classical data vectors into native quantum states to efficiently solve non-linear classification boundaries that remain completely intractable for classical supercomputers.22

## ---

**Problems**

Machine learning models are meticulously engineered to solve specific mathematical formulations of real-world challenges, strictly dictated by the nature of the target variable and the inherent topological structure of the domain.

## **Classification**

Classification models establish rigid decision boundaries to predict discrete categorical labels for a given set of input features. These techniques scale from elementary linear separations (like Logistic Regression) to highly complex, multi-dimensional non-linear hyperplanes (such as Support Vector Machines and deep networks), relying on the minimization of cross-entropy loss to maximize the posterior probability of the correct class assignment.23 **Notable Insights & Ciekawostki** Even strictly binary classification problems mathematically output continuous probability distributions, granting practitioners the vital flexibility to tune optimal decision thresholds based on the asymmetric real-world costs of false positives versus false negatives.24

## **Generative modeling**

Generative modeling focuses on learning the underlying joint probability distribution  or the pure data distribution  to autonomously synthesize entirely novel, statistically valid data samples.25 Unlike discriminative models that merely draw harsh boundaries between known classes, generative architectures like Generative Adversarial Networks (GANs) and Latent Diffusion models map chaotic latent noise spaces directly into the highly structured target data manifold.25

| Feature | Generative Modeling | Discriminative Modeling |
| :---- | :---- | :---- |
| **Objective** | Learns joint probability  | Learns conditional probability  |
| **Functionality** | Synthesizes new data points | Separates existing data points |
| **Complexity** | High (must model underlying distributions) | Lower (focuses only on boundaries) |
| **Architectures** | GANs, Diffusion Models, VAEs | SVM, Logistic Regression, Random Forest |

**Notable Insights & Ciekawostki** The modern explosion of ultra-realistic synthetic media (text-to-image) relies heavily on reversing complex Markov diffusion kernels, proving that iterative noise addition and stepwise denoising are perfectly symmetrical mathematical pathways.26

## **Regression**

Regression analysis models the continuous mathematical relationship between a dependent target variable and one or more independent feature variables.24 It establishes a deterministic equation by rigorously minimizing metrics such as Mean Squared Error (MSE) or Mean Absolute Error (MAE), projecting an optimal best-fit hyperplane through the multi-dimensional data scatter.24 **Notable Insights & Ciekawostki** While high-degree polynomial regression can fit highly complex and erratic curves perfectly, it severely risks catastrophic overfitting; thus, rigorous regularization terms (/) are mathematically enforced to penalize inflated parameter weights.24 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Linear\_regression.svg/800px-Linear\_regression.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Linear_regression.svg/800px-Linear_regression.svg.png))

## **Clustering**

Clustering algorithms partition massive, unlabeled datasets into cohesive, semantically meaningful groups strictly based on geometric proximity, spatial density, or shared variance. The mathematical objective is entirely internal: algorithms iteratively work to minimize intra-cluster distance while aggressively maximizing inter-cluster separation, relying heavily on explicit metrics like Euclidean distance or local gradient ascents.28 **Notable Insights & Ciekawostki** Fuzzy clustering introduces the profound operational concept that a data point does not belong rigidly to one single group, but rather maintains a continuous probability membership vector extending across all available clusters.29

## **Dimensionality reduction**

Dimensionality reduction systematically projects high-dimensional raw data into a much lower-dimensional subspace while carefully preserving maximal statistical variance or critical local topological neighbors.28 Linear techniques like Principal Component Analysis (PCA) utilize eigenvalue decomposition to find orthogonal components, whereas non-linear algorithms like t-SNE mathematically map complex geometric manifolds into 2D or 3D spaces strictly for human visualization.30 **Notable Insights & Ciekawostki** The "curse of dimensionality" dictates that as variable counts increase, the empty volume of the feature space expands exponentially, rendering standard distance metrics statistically sparse and useless—making reduction absolutely mandatory for large-scale analysis.32 !([https://upload.wikimedia.org/wikipedia/commons/thumb/f/f5/GaussianScatterPCA.svg/800px-GaussianScatterPCA.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f5/GaussianScatterPCA.svg/800px-GaussianScatterPCA.svg.png))

## **Density estimation**

Density estimation involves mathematically approximating an unobservable, continuous underlying probability density function based entirely on a finite set of discrete observed data points.33 Non-parametric algorithms like Kernel Density Estimation (KDE) place a smooth, continuous kernel (e.g., Gaussian) directly over every single discrete data point, summing these localized distributions to create a unified, continuous probability landscape.33 **Notable Insights & Ciekawostki** The selection of the smoothing parameter (kernel bandwidth) is critically sensitive: an overly narrow bandwidth causes severe under-smoothing (resulting in chaotic, spiky data), while an excessively wide bandwidth completely obliterates the true structural nuances of the distribution.33 !([https://upload.wikimedia.org/wikipedia/commons/thumb/4/41/Comparison\_of\_1D\_bandwidth\_selectors.png/800px-Comparison\_of\_1D\_bandwidth\_selectors.png](https://upload.wikimedia.org/wikipedia/commons/thumb/4/41/Comparison_of_1D_bandwidth_selectors.png/800px-Comparison_of_1D_bandwidth_selectors.png))

## **Anomaly detection**

Anomaly detection focuses on algorithmically identifying rare, highly suspicious data points that deviate significantly from the expected, established statistical norm.34 It leverages unsupervised and semi-supervised algorithms measuring local neighborhood point densities (Local Outlier Factor) or mapping isolation depths (Isolation Forests) to proactively isolate malicious network intrusions or predict imminent industrial equipment failure.35 **Notable Insights & Ciekawostki** In Isolation Forests, the mathematical premise of anomaly detection is brilliantly inverted: anomalous points are isolated and identified not by calculating density gradients, but by tracking how easily and quickly they are partitioned away from the normal data mass using random decision trees.34

## **Data cleaning**

Data cleaning automates the critical preprocessing phase, algorithmically imputing missing values, rectifying structural syntactic errors, and actively stripping statistical noise from datasets prior to executing any advanced modeling.38 Imputation methodologies range from simple -NN averaging—where missing features are reconstructed by borrowing from the closest geometric neighbors—to highly advanced, soft-impute low-rank matrix factorizations.38 **Notable Insights & Ciekawostki** Executing poor imputation logic instantly corrupts downstream dimensional reductions; utilizing small  values in -NN imputation generates highly volatile, noisy data, whereas larger  parameters effectively preserve the original macroscopic structure.38 !(https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Data\_cleaning\_cycle.png/800px-Data\_cleaning\_cycle.png)

## **AutoML**

Automated Machine Learning (AutoML) abstracts the notoriously difficult pipeline of hyperparameter tuning, model architecture selection, and feature engineering into an automated, high-level mathematical search problem. Utilizing sophisticated Bayesian optimization and evolutionary genetic algorithms, it iteratively probes the performance landscape, dynamically identifying the most efficient architecture and parameter set for a given arbitrary dataset.

**Notable Insights & Ciekawostki**

By thoroughly democratizing the mathematical mechanics of machine learning, AutoML decisively shifts the human practitioner's focus away from manual hyperparameter tweaking and redirects it toward curating high-quality, domain-specific data inputs.

## **Association rules**

Association rule learning explores and maps highly probable co-occurrence patterns between distinct categorical variables buried within massive transactional databases.39 Strictly governed by mathematical metrics such as *support*, *confidence*, and *lift*, algorithms (like Apriori and FP-Growth) iteratively scan datasets to deduce highly reliable conditional logic, represented functionally as .39

| Algorithm | Mechanism | Database Scans | Best Use Case |
| :---- | :---- | :---- | :---- |
| **Apriori** | Iterative candidate generation |  (high I/O cost) | Small datasets; foundational learning |
| **Eclat** | Depth-first search using TID-lists | Varies (fast intersection) | Medium datasets; speed prioritized |
| **FP-Growth** | Compact prefix-tree structure | 1 (highly optimized) | Massive big-data environments |

**Notable Insights & Ciekawostki** The FP-Growth algorithm vastly outpaces legacy systems like Apriori on massive datasets by generating a highly compact FP-tree structure, entirely eliminating the catastrophic memory cost of candidate generation and repeated database scans.39

## **Semantic analysis**

Semantic analysis algorithmically extracts latent thematic topics and syntactic structural meaning from massive, entirely unstructured text corpora.40 Techniques such as Latent Semantic Analysis (LSA) deploy Singular Value Decomposition (SVD) on vast document-term matrices to mathematically compress words that share identical contextual environments into highly dense, lower-dimensional vector spaces.41 **Notable Insights & Ciekawostki** This approach elegantly neutralizes the issue of polysemy (words possessing multiple meanings) by projecting terms into broad semantic concepts, empowering algorithms to detect sophisticated plagiarism even if the text has been aggressively paraphrased.42 !([https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Latent\_semantic\_analysis.png/800px-Latent\_semantic\_analysis.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Latent_semantic_analysis.png/800px-Latent_semantic_analysis.png))

## **Structured prediction**

Structured prediction resolves tasks where the output variables are inherently mutually dependent, forming complex internal structures like sequences, semantic trees, or Markov graphs.43 Unlike standard point-wise classification models, algorithms like Conditional Random Fields (CRF) compute the global joint probability distribution over an entire label sequence simultaneously, strictly enforcing localized context rules.45 **Notable Insights & Ciekawostki** In natural language processing, structured prediction is mandatory to ensure that a sentence's grammatical parts-of-speech are predicted as a logically interconnected sequence, rather than as isolated, mathematically independent probability events.44 !([https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Structured\_prediction\_graph.png/800px-Structured\_prediction\_graph.png](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9f/Structured_prediction_graph.png/800px-Structured_prediction_graph.png))

## **Feature engineering**

Feature engineering is the rigorous, domain-expertise-driven process of manually creating, scaling, or mathematically transforming input variables to expose underlying correlative relationships to the machine learning algorithm.47 It involves applying logarithmic normalizations, one-hot encoding categorical strings, and calculating high-degree polynomial interactions to artificially warp and expand the dataset's geometric feature space.48 **Notable Insights & Ciekawostki** Prior to the dominance of the deep learning era, the ultimate predictive accuracy of a classical machine learning model depended vastly more on brilliant, human-crafted feature engineering than on the specific algorithmic architecture employed.

## **Feature learning**

Feature learning (representation learning) fully automates the extraction of robust, hierarchical representations directly from raw, unscaled data, completely bypassing the need for manual human feature engineering. Deep neural networks naturally operate as advanced feature learners; early convolutional layers extract basic edges and gradients, while deeper layers autonomously compose them into highly abstract, semantic concepts.49 **Notable Insights & Ciekawostki** This paradigm directly unlocked the modern computer vision revolution, conclusively proving that data-driven features, mathematically optimized via backpropagation, invariably outperform even the most meticulously human-designed vision filters.50

## **Learning to rank**

Learning to rank constructs specialized machine learning models explicitly designed to sort and order a list of items optimally, forming the fundamental algorithmic engine behind modern web search, document retrieval, and recommender systems.46 The mathematical loss function evaluates data either pointwise (predicting absolute relevance scores), pairwise (determining the superior item between two options), or listwise (optimizing the entire permutation simultaneously using precision metrics).52 **Notable Insights & Ciekawostki** Because listwise inference often forms an extremely complex NP-hard quadratic assignment problem, researchers continuously engineer specialized star-structured interactions where scoring scales in polynomial time rather than exponential time.54 !([https://upload.wikimedia.org/wikipedia/commons/thumb/7/7b/Learning\_to\_rank.png/800px-Learning\_to\_rank.png](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7b/Learning_to_rank.png/800px-Learning_to_rank.png))

## **Grammar induction**

Grammar induction, or grammatical inference, utilizes machine learning to autonomously deduce formal syntactic rules and hierarchical grammatical structures directly from a raw set of observed strings or sequences.55 Shifting away from rigid, human-coded descriptive decision rules, it heavily utilizes distributional clustering and trial-and-error generative matching to construct highly complex, generative rewrite models.56 **Notable Insights & Ciekawostki** While algorithmically discovering flat linguistic sequences is computationally trivial, successfully inferring the recursive, hierarchical nature of natural human language without labeled constituent markup remains one of unsupervised learning's most profound mathematical puzzles.57

## **Ontology learning**

Ontology learning automatically extracts and maps complex, structured knowledge hierarchies—including classes, nested properties, and directed relational graphs—from raw text data to dynamically build semantic AI knowledge bases.58 It elevates models beyond isolated label prediction by structurally mapping dependencies (e.g., identifying "overtaking" or "parallel" logic in autonomous driving), enabling algorithms to execute advanced scenario-based reasoning.58 **Notable Insights & Ciekawostki** Executing successful ontology fusion empowers algorithms to reuse abstract, fundamental concepts across radically disparate domains, drastically accelerating AI model training while severely mitigating massive data duplication.58

## **Multimodal learning**

Multimodal learning ingests, fuses, and cross-correlates data from vastly disparate statistical distributions—such as visual inputs, raw audio, and text sequences—projecting them into a single, unified semantic latent space.59 Architectures like Flamingo deploy gated cross-attention mechanisms, freezing massive pretrained vision encoders and dynamically linking them with large language models to permit visually-prompted text generation and reasoning.60 **Notable Insights & Ciekawostki** By utilizing a meticulously controlled closed-gate mechanism, these multimodal models initially act purely as standard language models, slowly opening the gate to inject visual data, which elegantly avoids catastrophic interference during the fine-tuning phase.60

## ---

**Supervised learning**

Supervised algorithms construct deterministic mathematical mappings by rigorously analyzing historical, labeled data, generating boundaries or regression lines to dictate all future predictions.

## **Apprenticeship learning**

Apprenticeship learning observes an expert human demonstrator interacting with an environment to extract an implicit, unwritten reward function through a process called inverse reinforcement learning. Rather than executing a superficial behavioral clone of the human's exact actions, the algorithm mathematically derives *why* the human made those specific choices, permitting the agent to generalize the core behavior to entirely novel state spaces.

**Notable Insights & Ciekawostki**

This methodology drastically mitigates the critical alignment problem in complex robotics, as manually defining a flawless, exploit-proof reward equation for highly nuanced physical tasks is virtually impossible.

## **Decision trees**

Decision trees recursively partition the multidimensional feature space using consecutive boolean conditions, aiming to maximize information gain or strictly minimize Gini impurity at every juncture.62 The algorithm methodically routes data from a singular root node through internal decision splits, eventually terminating at isolated leaf nodes that represent the final classification or continuous regression value.62 **Notable Insights & Ciekawostki** While heavily vulnerable to severe variance and overfitting on complex datasets, their explicitly mapped, transparent if-then pathways make them the absolute gold standard for industries demanding total human interpretability.62 !([https://upload.wikimedia.org/wikipedia/commons/f/f3/Decision\_tree\_model.png](https://upload.wikimedia.org/wikipedia/commons/f/f3/Decision_tree_model.png))

## **Ensembles**

Ensemble learning aggregates the individual outputs of multiple independent, often weak base learners to formulate a highly robust, low-variance final prediction.23 This overarching meta-algorithmic strategy systematically attacks the bias-variance tradeoff by enforcing profound mathematical and structural diversity across the underlying models, effectively canceling out localized errors.23 **Notable Insights & Ciekawostki** Ensemble configurations consistently dominate global structured tabular data competitions, as the combined algorithmic consensus organically smooths out the severe eccentricities of individual sub-models.23

#### **Bagging**

Bootstrap aggregating (Bagging) generates multiple distinct training subsets through random sampling with replacement, training a parallel, independent model on each unique subset.23 The final predictive output is determined by calculating a deterministic average for regression or executing a strict majority vote for classification, systematically plummeting the variance of highly sensitive estimators.23 **Notable Insights & Ciekawostki** Because each internal model is trained on an artificially altered statistical distribution, extreme data outliers completely lose their power to aggressively skew the aggregate decision boundary.23 !([https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Bootstrap\_aggregating.png/800px-Bootstrap\_aggregating.png](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Bootstrap_aggregating.png/800px-Bootstrap_aggregating.png))

#### **Boosting**

Boosting trains a sequence of weak models strictly sequentially, where each subsequent model is heavily penalized to focus on correcting the mathematical residual errors generated by its immediate predecessor.23 The algorithm continually updates sample weights, heavily amplifying the loss penalty for misclassified data points as the cascade deepens, transforming a series of weak rules into a highly accurate strong learner.23 **Notable Insights & Ciekawostki** Historically met with deep skepticism by the classical statistical community, boosting algorithms (like Gradient Boosting and XGBoost) are now renowned for yielding the absolute highest predictive accuracy achievable on classical, non-perceptual data sets.64 !([https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Boosting\_algorithm.png/800px-Boosting\_algorithm.png](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Boosting_algorithm.png/800px-Boosting_algorithm.png))

#### **Random forest**

Random forests combine the variance-reduction of bagging with aggressive random feature selection, constructing hundreds of entirely uncorrelated decision trees.62 At every nodal split, only a randomized, restricted subset of features is evaluated, mathematically forcing the trees to remain structurally diverse and preventing dominant features from dictating every tree's architecture.62 **Notable Insights & Ciekawostki** This injected, mandatory randomness neutralizes the standard decision tree's inherent tendency to catastrophically overfit, yielding a robust classifier highly immune to background noise and scaling discrepancies.62 !([https://upload.wikimedia.org/wikipedia/commons/7/76/Random\_forest\_diagram\_complete.png](https://upload.wikimedia.org/wikipedia/commons/7/76/Random_forest_diagram_complete.png))

## **-NN**

-Nearest Neighbors (-NN) is a lazy, instance-based learning algorithm that commits the entire dataset to memory and classifies novel inputs based exclusively on the majority vote of their  closest neighbors in the high-dimensional feature space.23 It utilizes explicit distance metrics—such as Euclidean or Manhattan geometry—to compute proximity continuously during the inference phase.65 **Notable Insights & Ciekawostki** Because it completely lacks a traditional "training" phase, model compilation is effectively instantaneous; however, computational cost explodes exponentially during inference when calculating distances against massive datasets.65

## **Linear regression**

Linear regression mathematically fits a continuous, straight hyperplane through variables by strictly minimizing the sum of squared residual errors between the predictions and the true values.24 It models the functional dependency as a rigid, explicit linear combination: .23 **Notable Insights & Ciekawostki** The outputs of linear regression remain highly interpretable since the derived coefficients precisely dictate both the magnitude and the directional impact of each individual feature on the target variable.24 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Linear\_regression.svg/800px-Linear\_regression.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/3a/Linear_regression.svg/800px-Linear_regression.svg.png))

## **Naive Bayes**

Naive Bayes is a probabilistic classifier firmly rooted in Bayes' Theorem, calculating the conditional posterior probability  of a class given a set of features.23 It rigorously enforces a "naive" assumption that all input features are mathematically independent of one another given the class label, factorizing the joint probability into simple multiplications.23 **Notable Insights & Ciekawostki** Despite flagrantly violating real-world feature correlations, its extremely fast computation time and robust probability mapping made it historically dominant in NLP text classification and large-scale spam filtering.23 !([https://upload.wikimedia.org/wikipedia/commons/thumb/1/18/Bayes%27\_Theorem\_3D.png/800px-Bayes%27\_Theorem\_3D.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/18/Bayes%27_Theorem_3D.png/800px-Bayes%27_Theorem_3D.png))

## **Artificial neural networks**

Artificial Neural Networks (ANNs) cascade multiple layers of interconnected nodes (neurons) that apply non-linear activation functions to heavily weighted input sums.23 Trained via backpropagation and gradient descent optimization, they act as profound universal function approximators capable of modeling infinitely complex, non-linear data boundaries.66 **Notable Insights & Ciekawostki** The rediscovery and popularization of backpropagation in 1986 mathematically validated ANNs, permanently rescuing the field from the AI winter induced by the severe limitations of shallow networks.68

## **Logistic regression**

Logistic regression predicts binary class probabilities by passing a standard linear equation through a non-linear sigmoid activation function.23 It heavily restricts output values strictly to a bounded range of , modeling the log-odds of an event occurring via .24 **Notable Insights & Ciekawostki** Despite the explicit name "regression," it functions strictly as a classification algorithm, overwhelmingly favored in medical and financial sectors for returning smooth, highly calibrated probabilistic scores rather than blunt binary outputs.24 !([https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/Logistic\_regression.svg/800px-Logistic\_regression.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cb/Logistic_regression.svg/800px-Logistic_regression.svg.png))

## **Perceptron**

The Perceptron operates as the fundamental, primitive building block of modern neural architectures, outputting a strict binary decision based on a weighted sum processed through a harsh step function.66 Introduced theoretically in 1957, it iteratively updates network weights via simple error feedback whenever a misclassification event occurs.66 **Notable Insights & Ciekawostki** Its strict mathematical inability to solve the non-linear XOR problem temporarily halted AI progress for years, until the advent of Multi-Layer Perceptrons (MLPs) proved that architectural depth could easily bypass linear limitations.70

## **Relevance vector machine (RVM)**

The RVM applies a fully probabilistic, sparse Bayesian learning framework to generalized linear models, utilizing functional forms totally identical to Support Vector Machines.71 It inherently computes a covariance function , systematically estimating nuisance parameters and yielding a sparse solution space.72 **Notable Insights & Ciekawostki** By completely sidestepping the rigid, expensive hyperparameter cross-validation demands of SVMs, RVMs achieve extreme mathematical sparsity, relying on dramatically fewer basis functions while simultaneously providing true probabilistic outputs.71 !([https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/RVM\_concept.png/800px-RVM\_concept.png](https://upload.wikimedia.org/wikipedia/commons/thumb/4/4e/RVM_concept.png/800px-RVM_concept.png))

## **Support vector machine (SVM)**

SVM constructs a rigorous maximum-margin hyperplane that splits categorical classes with the absolute widest possible geometric separation.23 Through the mathematical "kernel trick," it implicitly elevates low-dimensional data into infinite-dimensional feature spaces, allowing perfectly straight hyperplanes to flawlessly separate highly non-linear data distributions.74 **Notable Insights & Ciekawostki** The vectors resting directly on the margin boundaries are the "Support Vectors"—meaning the trained model actively discards the vast majority of historical training data, relying exclusively on these critical edge cases for all future predictions.75 !([https://upload.wikimedia.org/wikipedia/commons/1/1b/SVM\_margin.png](https://upload.wikimedia.org/wikipedia/commons/1/1b/SVM_margin.png))

## ---

**Clustering**

Clustering algorithms autonomously discover intrinsic structures and cohesive groups within unannotated datasets by mapping geometrical similarities or probability densities.

## **BIRCH**

Balanced Iterative Reducing and Clustering using Hierarchies (BIRCH) dynamically compresses massive datasets into a high-density, highly compact "Clustering Feature (CF) Tree".77 By continually maintaining precise statistical summaries of data clusters directly in memory, it completely bypasses the computational need to iteratively scan the entire raw dataset.77 **Notable Insights & Ciekawostki** Its elegant  temporal complexity makes it unparalleled for analyzing billion-point datasets, though it struggles structurally when confronted with highly irregular, non-spherical clusters.78 !([https://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/BIRCH\_CF\_Tree.png/800px-BIRCH\_CF\_Tree.png](https://upload.wikimedia.org/wikipedia/commons/thumb/a/a2/BIRCH_CF_Tree.png/800px-BIRCH_CF_Tree.png))

## **CURE**

Clustering Using Representatives (CURE) entirely abandons single centroid logic, instead selecting a diverse, mathematically scattered set of representative points to capture and define a cluster's true morphological shape.78 These representative points are then physically shrunk toward the cluster's mathematical center by a fixed fraction to successfully dampen the gravitational pull of extreme, noisy outliers.78 **Notable Insights & Ciekawostki** Because it is not anchored to strict geometric variance limits or single centroids, CURE expertly navigates extreme, elongated, and highly asymmetrical data distributions that break traditional distance algorithms.78 !([https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/CURE\_clustering.png/800px-CURE\_clustering.png](https://upload.wikimedia.org/wikipedia/commons/thumb/c/cd/CURE_clustering.png/800px-CURE_clustering.png))

## **Hierarchical clustering**

Hierarchical clustering iteratively links distinct data points into nested groups, eventually forming a massive, tree-like dendrogram graph depicting all spatial relationships.80 Agglomerative approaches (bottom-up) systematically merge the closest geometric pairs, while divisive approaches (top-down) aggressively fracture large global clusters until total isolation is achieved.80 **Notable Insights & Ciekawostki** It elegantly eliminates the need to predefined the absolute number of clusters ; practitioners simply cut the resulting dendrogram horizontally at their desired level of semantic or geometric granularity.81

## **-means**

-means rigidly partitions data into exactly  predefined clusters by repeatedly calculating data centroids and reassigning points to strictly minimize total within-cluster variance.23 The algorithm aggressively converges to a local minimum, operating solely on geometric mean proximity.82 **Notable Insights & Ciekawostki** While exceptionally scalable and computationally light, its strict mathematical reliance on spherical cluster geometries means it aggressively absorbs noisy outliers and entirely fails on nested or ring-shaped topological datasets.28

## **Fuzzy clustering**

Fuzzy c-means replaces hard binary cluster boundaries with a soft, probabilistic membership matrix, mathematically calculated via the objective function .84 Every data point connects to every centroid with a fractional weight \$w \\in \$, strictly controlled by a fuzzifier parameter .84 **Notable Insights & Ciekawostki** This gradient logic perfectly mirrors ambiguous biological data—like an apple exhibiting both red and green qualities simultaneously—rather than forcing points into rigid, arbitrary statistical categories.29

## **Expectation–maximization (EM)**

EM algorithms alternate meticulously between estimating latent probability distributions (E-step) and mathematically maximizing the parameters of those distributions (M-step). In advanced clustering, it serves as the generalized probabilistic framework powering Gaussian Mixture Models (GMM), utilizing soft-assignments based strictly on posterior likelihoods rather than harsh distance thresholds.80 **Notable Insights & Ciekawostki** EM algorithms naturally map covariance matrices, allowing them to perfectly capture highly elliptical, stretched data distributions that rigidly circular models like -means completely misinterpret.80

## **DBSCAN**

Density-Based Spatial Clustering of Applications with Noise (DBSCAN) defines true clusters as continuous regions of high density explicitly separated by regions of low spatial density.28 It chains core points together within a strict geometric  radius, automatically classifying points lingering in sparse areas as pure unadulterated noise.28 **Notable Insights & Ciekawostki** It natively discovers clusters of arbitrary and highly complex topological shapes (like interlocking spirals or nested rings) without requiring the user to blind-guess the number of clusters in advance.28 !([https://upload.wikimedia.org/wikipedia/commons/a/af/DBSCAN-Illustration.svg](https://upload.wikimedia.org/wikipedia/commons/a/af/DBSCAN-Illustration.svg))

## **OPTICS**

Ordering Points To Identify the Clustering Structure (OPTICS) directly remedies DBSCAN's inherent vulnerability to datasets featuring highly variable data densities.86 It constructs a mathematically ordered database based strictly on "reachability distance," effectively allowing the algorithm to analyze the clustering structure at multiple  scales simultaneously without recalculation.86 **Notable Insights & Ciekawostki** The resulting 2D reachability plot visually simulates a topographical valley map, where the deepest valleys vividly represent the tightest, densest data clusters across dynamically changing granularities.86 !([https://upload.wikimedia.org/wikipedia/commons/f/f9/OPTICS.svg](https://upload.wikimedia.org/wikipedia/commons/f/f9/OPTICS.svg))

## **Mean shift**

Mean shift is a highly robust sliding-window, mode-seeking algorithm that continuously shifts centroids dynamically toward regions of maximum data density. By repeatedly calculating the geometric center of gravity within a given radius, points naturally climb the gradient of the underlying probability density function until they reach a state of mathematical convergence.

**Notable Insights & Ciekawostki**

Highly popular in computer vision for image segmentation and real-time object tracking, it organically determines absolute cluster counts based strictly on the terrain of the data, completely ignoring pre-set parameter limits.

!([https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Mean\_shift\_clustering.png/800px-Mean\_shift\_clustering.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Mean_shift_clustering.png/800px-Mean_shift_clustering.png))

## ---

**Dimensionality reduction**

Dimensionality reduction systematically compresses wide feature spaces to combat the curse of dimensionality, preserving maximum statistical variance or distinct spatial topology while rendering the data computationally viable.

## **Factor analysis**

Factor analysis systematically isolates underlying, unobservable latent variables (factors) that cause the measured, observed variables to covary.31 Unlike pure data compression algorithms, it operates under a strict causal assumption where the observed features are constructed as direct linear combinations of hidden foundational concepts.87 **Notable Insights & Ciekawostki** Extensively deployed in psychometrics and the social sciences, it successfully distinguishes intrinsic, invisible underlying traits (e.g., "general intelligence") from the chaotic surface-level noise of standardized test scores.87

## **CCA**

Canonical Correlation Analysis (CCA) uncovers complex linear mathematical relationships between two entirely different sets of multidimensional variables (views) extracted from the exact same entities.88 It derives projection vectors for both distinct sets that mathematically maximize the cross-correlation between their resulting lower-dimensional components.88 **Notable Insights & Ciekawostki** Unlike naive feature stacking, CCA explicitly maps the structural, mathematical bridge between highly disparate domains—for instance, directly correlating a patient's complex genomic profile with their localized brain MRI scans.88

## **ICA**

Independent Component Analysis (ICA) forcefully decomposes a multivariate, overlapping signal into additive, mathematically independent, and strictly non-Gaussian subcomponents.28 It operates on the blind source separation assumption that observed data mixes multiple separate origin signals, prioritizing absolute statistical independence over mere lack of correlation.28 **Notable Insights & Ciekawostki** This serves as the definitive mathematical solution to the classic "Cocktail Party Problem," seamlessly untangling a single chaotic audio track into perfectly isolated voices by enforcing maximum statistical non-Gaussianity.28

## **LDA**

Linear Discriminant Analysis (LDA) is a supervised dimensional reduction technique that projects data vectors to maximize the spatial variance *between* distinct classes while aggressively minimizing the geometric variance *within* them.89 It explicitly utilizes ground-truth class labels to calculate complex between-class and within-class scatter matrices.89

| Feature | PCA | LDA |
| :---- | :---- | :---- |
| **Type** | Unsupervised | Supervised |
| **Objective** | Maximize total data variance | Maximize class separability |
| **Label Dependency** | Ignores labels | Heavily relies on accurate labels |
| **Max Dimensions** | **** original features |  (number of classes - 1) |

**Notable Insights & Ciekawostki** It acts as a profound mathematical preprocessing step for classification; unlike PCA which blindly follows variance, LDA actively distorts the feature space to ensure different categorical groups are cleanly severable.89 !([https://upload.wikimedia.org/wikipedia/commons/thumb/4/41/LDA\_projection.png/800px-LDA\_projection.png](https://upload.wikimedia.org/wikipedia/commons/thumb/4/41/LDA_projection.png/800px-LDA_projection.png))

## **NMF**

Non-negative Matrix Factorization (NMF) decomposes a strictly positive raw data matrix into two lower-rank matrices ( and ) that contain exclusively non-negative elements.91 This hard mathematical restriction guarantees that the original data is reconstructed via purely additive (parts-based) components, strictly blocking any negative cancellations.91 **Notable Insights & Ciekawostki** This creates highly interpretable, distinct feature subsets—for example, automatically breaking a human face down into discrete eyes, noses, and lips rather than the abstract mathematical noise generated by PCA.91

## **PCA**

Principal Component Analysis (PCA) algorithmically transforms dataset vectors into a new orthogonal coordinate system where the first axis captures the absolute maximum variance, and subsequent axes capture progressively diminishing variances.23 Achieved natively via Singular Value Decomposition (SVD), it creates principal components that are highly compact, uncorrelated linear combinations of the original raw features.28 **Notable Insights & Ciekawostki** While exceptionally efficient computationally, its strictly linear nature means it utterly fails to unroll complex manifold structures (like the famed Swiss Roll dataset), merely squashing them flat and destroying topology.38

## **PGD**

Proper Generalized Decomposition (PGD) circumvents the curse of dimensionality in Partial Differential Equations (PDEs) by constructing an a priori separated representation of complex field functions.92 It approximates high-dimensional solutions through successive algorithmic enrichment, summing univariate functions  across spatial coordinates iteratively.93 **Notable Insights & Ciekawostki** PGD generates a "computational vademecum"—a highly dense meta-model holding all particular solutions for all possible parametric variables, acting as a profound, instant look-up map for ultra-complex mechanics.93 !([https://upload.wikimedia.org/wikipedia/commons/thumb/7/77/Proper\_generalized\_decomposition.png/800px-Proper\_generalized\_decomposition.png](https://upload.wikimedia.org/wikipedia/commons/thumb/7/77/Proper_generalized_decomposition.png/800px-Proper_generalized_decomposition.png))

## **t-SNE**

t-distributed Stochastic Neighbor Embedding (t-SNE) constructs complex probability distributions over point pairs, rigorously minimizing the Kullback–Leibler (KL) divergence between the high-dimensional original space and the low-dimensional projection.30 It utilizes a Gaussian kernel in the original space but intentionally employs a heavy-tailed Student’s t-distribution in the projection to prevent points from catastrophically crowding the map's center.30 **Notable Insights & Ciekawostki** By obsessively preserving local topological structure at the severe expense of global distances, it forces distinct clusters into vivid islands, absolutely dominating modern biomedical and genomic visualizations.30 !([https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/T-SNE\_and\_PCA.png/800px-T-SNE\_and\_PCA.png](https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/T-SNE_and_PCA.png/800px-T-SNE_and_PCA.png))

## **SDL**

Sparse Dictionary Learning (SDL) learns a highly optimized, over-complete set of basis elements (atoms) to represent complex signals as a sparse linear combination, mapping data into higher-dimensional but computationally simpler discrete forms.96 It optimizes a delicate trade-off loss function enforcing strict residual minimization meticulously balanced against an aggressive penalty forcing deep sparsity on the coefficient vectors.97 **Notable Insights & Ciekawostki** Unlike statically predefined wavelets, custom-trained SDL dictionaries compress image and audio signals to incredible extremes, forming the backbone of advanced compressed sensing and aggressive image denoising.96 !([https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/Sparse\_dictionary\_learning.png/800px-Sparse\_dictionary\_learning.png](https://upload.wikimedia.org/wikipedia/commons/thumb/5/5b/Sparse_dictionary_learning.png/800px-Sparse_dictionary_learning.png))

## ---

**Structured prediction**

Structured prediction models output sequences or graphs of interdependent variables, enforcing localized structural rules and sequential contexts that isolated classifiers inherently ignore.

## **Graphical models**

Graphical models construct complex probabilistic landscapes using nodes to represent variables and edges to represent distinct conditional dependencies. They leverage rigorous graph theory to factorize massive joint probability distributions into vastly smaller, localized, and computationally tractable subsets.43

#### **Bayes net**

Bayesian networks are directed acyclic graphs (DAGs) explicitly representing joint probability distributions through strictly defined node dependencies.43 They rely on rigorous conditional independence assumptions to mathematically factorize complex global probabilities into simple local multiplicative chains.44 **Notable Insights & Ciekawostki** Their intuitive modularity makes them phenomenal at incorporating raw expert domain knowledge, perfectly bridging the gap between data-driven statistical inference and explicit causal mapping.43 !([https://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Simple\_Bayesian\_Network.png/800px-Simple\_Bayesian\_Network.png](https://upload.wikimedia.org/wikipedia/commons/thumb/0/0e/Simple_Bayesian_Network.png/800px-Simple_Bayesian_Network.png))

#### **Conditional random field**

Conditional Random Fields (CRFs) are undirected, discriminative graphical models that compute the conditional probability  of a global label sequence given the entire input context.45 They normalize arbitrary potential functions over feature cliques across the entire sequence simultaneously, totally eliminating localized probability constraints.98 **Notable Insights & Ciekawostki** By deliberately discarding the rigid independence assumptions of HMMs, CRFs flawlessly model overlapping contextual features, previously achieving unmatched state-of-the-art results in NLP part-of-speech tagging.45 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/CRF\_graph.png/800px-CRF\_graph.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/CRF_graph.png/800px-CRF_graph.png))

#### **Hidden Markov model**

Hidden Markov Models (HMMs) are first-order generative sequence models where observable events are probabilistically generated by an underlying chain of latent "hidden" states.44 They assume a strict Markov property—the next hidden state depends exclusively on the current state, completely ignoring deep historical sequence data.44 **Notable Insights & Ciekawostki** They are decoded via the famous Viterbi dynamic programming algorithm, which brilliantly finds the exact sequence of underlying states most likely to have generated the observed data without exploring every mathematical path.101

## ---

**Anomaly detection**

Anomaly detection isolates statistically malformed data records, requiring highly specialized models that excel at quantifying spatial rarity and geometric deviation.

## **RANSAC**

Random Sample Consensus (RANSAC) is a stochastic, non-deterministic algorithm that iteratively fits a mathematical model using random, minimal subsets of the data.103 It categorizes all non-selected points as either supportive "inliers" or destructive "outliers" based on a strict error threshold, eventually storing the model exhibiting the highest consensus score.105 **Notable Insights & Ciekawostki** It elegantly bypasses traditional least-squares regressions that mathematically drag the best-fit line toward extreme outliers, making it absolutely indispensable in robust computer vision mosaicking.104 !([https://upload.wikimedia.org/wikipedia/commons/c/c0/RANSAC\_01.png](https://upload.wikimedia.org/wikipedia/commons/c/c0/RANSAC_01.png))

## **-NN**

In anomaly detection, -NN evaluates the absolute geometric distance of a point to its  nearest neighbor within the feature space.36 If a data point sits in an isolated spatial void far removed from dense clusters, its neighbor distance score spikes exponentially, mathematically flagging it as an outlier.34 **Notable Insights & Ciekawostki** While highly effective locally, setting a uniform global distance threshold fails completely when dealing with a dataset containing multiple clusters exhibiting vastly different internal densities.

## **Local outlier factor**

Local Outlier Factor (LOF) completely solves the multi-density problem by computing the ratio of a point's local density against the average densities of its -neighbors.36 Using the formula , a score significantly greater than 1 mathematically flags the point as substantially sparser than its immediate peers.36 **Notable Insights & Ciekawostki** Because it treats anomaly scores relative to micro-neighborhoods, it flawlessly catches subtle outliers nestled near tight clusters that global algorithms completely overlook.37

## **Isolation forest**

Isolation Forests assemble massive ensembles of random decision trees targeting explicit data isolation rather than mapping normal geometric profiles.34 Because anomalies are rare and spatially distinct, random recursive feature splitting isolates them into shallow leaf nodes much faster (lower tree depth) than normal dense points.35 **Notable Insights & Ciekawostki** It is one of the few anomaly algorithms possessing pure linear time complexity and remarkably low memory demands, excelling directly in high-dimensional corporate fraud detection pipelines.34

## ---

**Neural networks**

Neural networks utilize continuous, mathematically differentiable parameterized graphs to model extremely complex abstractions, forming the absolute backbone of modern representation learning.

## **Autoencoder**

Autoencoders purposefully compress data into a low-dimensional structural bottleneck (the latent space) and then algorithmically attempt to reconstruct the original input exactly. This severe mathematical restriction forces the network to discard noise and isolate the most fundamental generative features, acting effectively as a powerful, non-linear form of PCA.107 **Notable Insights & Ciekawostki** Variational Autoencoders (VAEs) force the latent space to map to pure Gaussian probability distributions, allowing practitioners to mathematically interpolate and morph smoothly between generated states.80

## **Deep learning**

Deep learning refers strictly to neural architectures featuring multiple consecutive hidden layers, stacking localized abstractions into global comprehension.23 These dense mathematical pipelines learn feature hierarchies recursively; early layers detect simple gradients, mid-layers assemble textures, and final layers understand full semantic concepts.23 **Notable Insights & Ciekawostki** Its modern viability and explosive dominance were rescued entirely by the adoption of the ReLU activation function, which stopped the notorious "vanishing gradient" problem from destroying mathematical feedback in deep architectures.66 !([https://upload.wikimedia.org/wikipedia/commons/thumb/e/e4/Artificial\_neural\_network.svg/800px-Artificial\_neural\_network.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e4/Artificial_neural_network.svg/800px-Artificial_neural_network.svg.png))

## **Feedforward neural network**

The feedforward network is the most primitive topology, where mathematical activation flows exclusively forward from input to output.107 Data passes directly through dense weight matrices without any recurrent cycles or temporal memory loops, making them highly efficient but blind to sequential timing.23 **Notable Insights & Ciekawostki** Under the Universal Approximation Theorem, a feedforward network with a single, infinitely wide hidden layer can theoretically model any continuous function, though it is highly inefficient practically.109

## **Recurrent neural network**

Recurrent Neural Networks (RNNs) pass the output of hidden layers back into themselves via recurrent loops, granting the architecture temporal memory to parse sequential data.110 They suffer heavily from vanishing gradients over long spans, as recursive matrix multiplications cause mathematical updates to exponentially decay.112

#### **LSTM**

Long Short-Term Memory (LSTM) networks cure the RNN vanishing gradient problem by introducing an internal continuous memory "cell state" explicitly protected by specialized gates.113 Forget, input, and output gates utilize sigmoid multipliers to explicitly dictate what information should mathematically persist or be obliterated across long time spans.112 **Notable Insights & Ciekawostki** Because gradients flow freely through the central cell state without aggressive multiplication, LSTMs effectively broke the barrier on machine translation and speech synthesis before 2017.112 !([https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/LSTM\_Cell.svg/800px-LSTM\_Cell.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/9/93/LSTM_Cell.svg/800px-LSTM_Cell.svg.png))

#### **GRU**

Gated Recurrent Units (GRUs) consolidate the complex architecture of LSTMs by merging the cell state and hidden state, combining the forget and input gates into a single mathematical "update" gate.112 This reduces the total tensor parameter count significantly while providing virtually identical predictive performance on short sequence tasks.112 **Notable Insights & Ciekawostki** This architectural streamlining makes them the preferred recurrent model in low-compute and edge-device hardware applications, achieving equivalent accuracy with lower energy costs.112 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/37/Gated\_Recurrent\_Unit.svg/800px-Gated\_Recurrent\_Unit.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/37/Gated_Recurrent_Unit.svg/800px-Gated_Recurrent_Unit.svg.png))

#### **ESN**

An Echo State Network (ESN) passes sequential data into a massive, fixed, randomized dynamical "reservoir" of neurons with an explicitly defined spectral radius to maintain the Echo State Property.114 The internal weights are entirely frozen; only the final linear readout weights are actually trained via simple regression, bypassing backpropagation through time entirely.114 **Notable Insights & Ciekawostki** By treating the chaotic random network as an excitable physical medium, it achieves immense temporal context tracking at a tiny fraction of the standard computational cost.114 !([https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Echo\_State\_Network.png/800px-Echo\_State\_Network.png](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Echo_State_Network.png/800px-Echo_State_Network.png))

#### **Reservoir computing**

Reservoir computing is the overarching paradigm containing ESNs, exploiting high-dimensional nonlinear dynamics without training internal weights.114 It maps input signals into a higher-dimensional computational space (the reservoir), which organically provides a rich set of temporal features.114 **Notable Insights & Ciekawostki** This concept bridges deep learning and physics, actively inspiring optical and quantum reservoirs where actual physical materials (like water ripples or laser states) compute the hidden layers.114 !([https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Reservoir\_computing.png/800px-Reservoir\_computing.png](https://upload.wikimedia.org/wikipedia/commons/thumb/8/87/Reservoir_computing.png/800px-Reservoir_computing.png))

## **Boltzmann machine**

Boltzmann machines are stochastic, undirected, energy-based neural networks that utilize thermodynamic principles to model the probability distribution of their inputs. They update node states via simulated annealing, mathematically striving to find the global minimum energy state of the entire network.

#### **Restricted**

Restricted Boltzmann Machines (RBMs) are shallow variants featuring a visible layer and a hidden layer, with strictly zero intra-layer connections.50 They train probabilistically using Contrastive Divergence and Gibbs sampling, adjusting weights to map the probability distribution of the input data.49 **Notable Insights & Ciekawostki** While mostly deprecated today, Geoffrey Hinton's work on stacking RBMs into Deep Belief Networks was the pivotal spark that officially initiated the modern Deep Learning boom.51 !([https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Restricted\_Boltzmann\_machine.svg/800px-Restricted\_Boltzmann\_machine.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e8/Restricted_Boltzmann_machine.svg/800px-Restricted_Boltzmann_machine.svg.png))

## **GAN**

Generative Adversarial Networks (GANs) pit two distinct neural networks against each other in a zero-sum mathematical game: a Generator synthesizing fake data, and a Discriminator attempting to flag it.51 The Generator's loss function explicitly leverages the Discriminator's gradients to adaptively improve, driving the generated outputs toward statistical perfection.38 **Notable Insights & Ciekawostki** The delicate balance required to stop one network from overpowering the other often leads to "mode collapse," a notorious failure where the generator hallucinates the exact same output repeatedly.25

## **Diffusion model**

Diffusion models slowly obliterate input data via a forward Markov process of injecting Gaussian noise, learning to mathematically reverse this trajectory to recover the original state.25 Starting from pure statistical static, the learned U-Net backbone iteratively deducts fractional noise gradients to sculpt extremely high-fidelity outputs.25 **Notable Insights & Ciekawostki** This incredibly stable iterative denoising bypassed GAN mode collapse, becoming the commercial backbone for industry-dominating generative models like Midjourney, DALL-E, and Stable Diffusion.25 !([https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Diffusion\_Diagram.png/800px-Diffusion\_Diagram.png](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Diffusion_Diagram.png/800px-Diffusion_Diagram.png))

## **SOM**

Self-Organizing Maps (SOMs) use competitive unsupervised learning to reduce high-dimensional data onto a 2D topological grid.117 Nodes fiercely compete, and the winning "Best Matching Unit" (BMU) mathematically pulls its topological neighbors closer to the input data during training.118 **Notable Insights & Ciekawostki** Unlike standard backpropagation, this explicitly mimics biological cortical mapping, pulling weight vectors physically through the data space without a pre-defined loss gradient.117 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Self-organizing\_map.png/800px-Self-organizing\_map.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Self-organizing_map.png/800px-Self-organizing_map.png))

## **Convolutional neural network**

Convolutional Neural Networks (CNNs) utilize overlapping spatial filters (kernels) to convolve across input grids, maintaining deep spatial awareness through weight-sharing rather than indiscriminately flattening data.111 Feature mapping is paired with pooling layers to aggressively downsample dimensionality while achieving vital translational invariance.108 **Notable Insights & Ciekawostki** The inception of CNN architectures definitively ended the reliance on human-crafted image filters, proving that algorithms could learn optimal, localized texture detection independently.66

#### **U-Net**

U-Net utilizes an elegant, symmetric expansive path with skip connections to perfectly align highly detailed pixel segmentations for complex imagery.121 The skip connections bypass the bottleneck, feeding high-resolution structural context directly into the upsampling decoder.121 **Notable Insights & Ciekawostki** Originally designed exclusively for medical segmentation, its structure is so robust it now forms the denoising backbone of all major generative Diffusion models.121

#### **LeNet**

LeNet, introduced in the 1990s by Yann LeCun, was the pioneering architecture that officially proved deep convolutional neural networks were viable in practice.66 It established the fundamental blueprint of cascading convolution, pooling, and fully connected layers.66 **Notable Insights & Ciekawostki** Its historical application in decoding handwritten bank checks for the US Postal Service was one of the first commercially successful deployments of deep learning.66

#### **AlexNet**

AlexNet (2012) drastically scaled the LeNet concept using GPUs, dominating the ImageNet classification challenge.66 It introduced ReLU activations to stop vanishing gradients and deployed Dropout to control aggressive overfitting.66 **Notable Insights & Ciekawostki** AlexNet's unprecedented accuracy jump over classical algorithms fundamentally triggered the absolute pivot of the entire AI industry toward deep neural networks.66

#### **DeepDream**

DeepDream reverses the standard CNN pipeline, applying gradient ascent to explicitly maximize specific neural activations.107 By iteratively adjusting the input image rather than the weights, it forces the network to hallucinate deeply surreal, algorithmic patterns.107 **Notable Insights & Ciekawostki** Originally created as a diagnostic tool to visualize exactly what specific neurons were learning, it inadvertently spawned a massive algorithmic art movement. !([https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/DeepDream\_image\_example.jpg/800px-DeepDream\_image\_example.jpg](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e0/DeepDream_image_example.jpg/800px-DeepDream_image_example.jpg))

## **Neural field**

Neural fields bypass discrete grid tokens (like pixels or voxels) to model entirely continuous spatial mappings.109 They map continuous inputs (coordinates, time) directly to continuous scalar or vector outputs, rendering them fully discretization-independent and easily differentiable.109 **Notable Insights & Ciekawostki** Dealing with continuous data mathematically allows for a staggering reduction in spatial complexity, resulting in incredibly lightweight networks compared to discrete CNNs.109

#### **Neural radiance field**

Neural Radiance Fields (NeRF) train an MLP to output localized volumetric density and view-dependent emitted radiance based purely on  spatial coordinates and camera angles.123 By sampling points along camera rays, traditional volume rendering techniques mathematically synthesize flawless novel 2D views.124 **Notable Insights & Ciekawostki** NeRF fundamentally disrupted computer graphics, allowing photorealistic 3D scene reconstruction from a sparse handful of 2D images without complex mesh modeling.124 !([https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Neural\_radiance\_field\_scene\_representation.png/800px-Neural\_radiance\_field\_scene\_representation.png](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/Neural_radiance_field_scene_representation.png/800px-Neural_radiance_field_scene_representation.png))

#### **Physics-informed neural networks**

Physics-Informed Neural Networks (PINNs) explicitly inject known Partial Differential Equations (PDEs) directly into the model's loss function.125 This rigorous mathematical penalty forces the neural predictions to rigorously obey fundamental physics parameters like mass and fluid incompressibility.125 **Notable Insights & Ciekawostki** By hardcoding physical laws as a loss penalty, PINNs demand exponentially fewer training data points than naive statistical networks to model fluid flows or heat transfer.126

## **Transformer**

Transformers abandon sequential recurrence entirely for globally parallel Self-Attention, allowing tokens to mathematically "attend" to every other token simultaneously to derive rich context.108 This parallel architecture means GPUs can be utilized with maximum efficiency, radically accelerating calculations and training times.108 **Notable Insights & Ciekawostki** By utterly shattering sequential scaling laws via GPU parallelization, Transformers fueled the creation of GPT and established complete dominance over the natural language domain.128 !([https://upload.wikimedia.org/wikipedia/commons/9/91/Full\_GPT\_architecture.svg](https://upload.wikimedia.org/wikipedia/commons/9/91/Full_GPT_architecture.svg))

#### **Vision transformer**

Vision Transformers (ViT) slice flat 2D images into segmented grid patches, passing them through linear embeddings exactly like linguistic words.120 They apply the exact same multi-head attention mechanisms, allowing the network to build a global understanding of the image instantaneously rather than convolving locally.120 **Notable Insights & Ciekawostki** ViTs mathematically erased the boundaries between text and vision processing, proving that massive datasets and attention outscale the inductive bias of convolutions. !([https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/Vision\_Transformer.png/800px-Vision\_Transformer.png](https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/Vision_Transformer.png/800px-Vision_Transformer.png))

## **Mamba**

Mamba revolutionizes sequence modeling via Structured State Space Models (SSMs) by mathematically varying internal state parameters  dynamically per token.129 It executes selective memory propagation using a highly optimized hardware-aware parallel scan, achieving  higher inference throughput while scaling linearly rather than quadratically.130 **Notable Insights & Ciekawostki** By completely abandoning the core Attention module, Mamba elegantly solves the quadratic bottleneck of Transformers, effectively processing millions of tokens in extreme contexts like genomics and high-res audio.130

## **Spiking neural network**

Spiking Neural Networks (SNNs) communicate strictly through sparse, asynchronous electrical bursts (spikes), flawlessly mimicking human biological integrate-and-fire energy dynamics.107 The neurons maintain a membrane potential, only firing when an explicit mathematical threshold is breached. **Notable Insights & Ciekawostki** Because data is transmitted asynchronously and sparsely, SNNs consume exponentially less power than continuous ANNs, making them the ultimate frontier for low-power edge computing. !([https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/Spiking\_neural\_network.png/800px-Spiking\_neural\_network.png](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/Spiking_neural_network.png/800px-Spiking_neural_network.png))

## **Memtransistor**

Memtransistors are multi-terminal neuromorphic devices that blend the data-storage capabilities of a memristor with the gating logic of a transistor. They allow for complex, continuous weight tuning directly at the hardware node, completely unifying storage and compute.

**Notable Insights & Ciekawostki**

This explicitly eliminates the massive energy cost of shuffling data between a CPU and RAM, fundamentally breaking the Von Neumann bottleneck.

## **Electrochemical RAM (ECRAM)**

Electrochemical RAM (ECRAM) utilizes ion intercalation to alter the electrical resistance of the channel, creating analog non-volatile memory designed explicitly as synaptic weights for AI.18 It provides inherently symmetric and deterministic conductance programming, which is critical for executing accurate backpropagation on analog chips.18 **Notable Insights & Ciekawostki** ECRAM presents the most viable physical pathway to infinitely scaling artificial intelligence while averting the looming global energy crisis induced by massive digital GPU clusters.18 !([https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Electrochemical\_Random\_Access\_Memory.png/800px-Electrochemical\_Random\_Access\_Memory.png](https://upload.wikimedia.org/wikipedia/commons/thumb/b/b5/Electrochemical_Random_Access_Memory.png/800px-Electrochemical_Random_Access_Memory.png))

## ---

**Reinforcement learning**

Reinforcement learning shifts from rigid classification mapping to active behavioral strategy, mathematically teaching autonomous agents to optimize cumulative numerical rewards inside highly dynamic simulations.

## **Q-learning**

Q-learning is an off-policy, value-based algorithm that dynamically updates a Q-table containing explicit state-action quality scores.132 It utilizes the Bellman equation  to assume the absolute optimal future pathway, entirely regardless of the agent's current exploration strategy.132 **Notable Insights & Ciekawostki** Because it is off-policy, the algorithm explicitly separates the sub-optimal policy generating the exploratory behavior from the aggressive, mathematically optimal policy it aims to solidify.133

## **Policy gradient method**

Policy gradient methods bypass explicit state-value mapping and deploy gradient ascent to directly tweak the probabilities of the actions dictating the continuous policy .134 When a simulated trajectory results in a high return, the mathematical likelihood of every specific action taken in that trajectory is uniformly bumped up via gradient ascent.135 **Notable Insights & Ciekawostki** Unlike value-based fitting, this operates strictly on-policy, experiencing significantly higher mathematical variance but vastly superior stability when deployed in continuous, infinite-action environments like robotics.135

## **SARSA**

State–Action–Reward–State–Action (SARSA) is a highly conservative, on-policy variant of Q-learning.132 Instead of mathematically assuming the absolute maximum future reward, its update explicitly incorporates the reward of the action that the agent's -greedy policy *actually* took: .133 **Notable Insights & Ciekawostki** This cautious alignment prevents algorithmic agents from hallucinating high rewards across hazardous pathways (like navigating close to a cliff edge), keeping them much safer during early physical training.132 !([https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/SARSA\_algorithm.png/800px-SARSA\_algorithm.png](https://upload.wikimedia.org/wikipedia/commons/thumb/e/e5/SARSA_algorithm.png/800px-SARSA_algorithm.png))

## **Temporal difference (TD)**

Temporal difference learning fundamentally blends Monte Carlo testing with dynamic programming to bootstrap reward estimates over time.136 Instead of waiting for an entire episode to conclude to calculate return, it mathematically updates predictions step-by-step using the immediate reward and the next state's predicted value (the TD Error).136 **Notable Insights & Ciekawostki** This explicit error calculation precisely mirrors how biological dopamine systems dynamically adapt expectations in the human brain when exposed to unanticipated rewards.136 !([https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/Temporal\_Difference\_Learning.png/800px-Temporal\_Difference\_Learning.png](https://upload.wikimedia.org/wikipedia/commons/thumb/b/ba/Temporal_Difference_Learning.png/800px-Temporal_Difference_Learning.png))

## **Multi-agent**

Multi-agent reinforcement learning (MARL) subjects multiple simultaneous agents to a shared mathematical environment to observe emergent sociological behaviors like extreme cooperation, reciprocity, or aggressive sabotage.137 The reinforcement algorithms must calculate policies that continuously adapt to the shifting strategies of other intelligent, unpredictable entities.137 **Notable Insights & Ciekawostki** The conflict between the immediate needs of an individual agent and the long-term optimal strategy of the group bridges pure mathematical optimization with advanced behavioral game theory.137 !([https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/Multi-agent\_reinforcement\_learning.png/800px-Multi-agent\_reinforcement\_learning.png](https://upload.wikimedia.org/wikipedia/commons/thumb/8/83/Multi-agent_reinforcement_learning.png/800px-Multi-agent_reinforcement_learning.png))

#### **Self-play**

In Self-play, an algorithm iteratively refines its policy by battling concurrent clones or historical iterations of itself within the environment.138 This mathematically guarantees that the agent is always challenged by an opponent of perfectly matched skill. **Notable Insights & Ciekawostki** Self-play forces an algorithm into an infinite, escalating curriculum loop, historically unlocking superhuman intelligence in systems like AlphaGo and OpenAI Five.140 !([https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/Self-play\_loop.png/800px-Self-play\_loop.png](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/Self-play_loop.png/800px-Self-play_loop.png))

## ---

**Learning with humans**

Human-centric paradigms actively guide raw mathematical algorithms using qualitative feedback, successfully resolving tasks where rigid algorithmic objectives are subjective, highly nuanced, or physically hazardous.

## **Active learning**

Active learning aggressively flips the data curation pipeline by empowering the model to proactively query the human annotator for labels on highly specific, statistically uncertain data points.141 Using methods like "uncertainty sampling," the model ignores redundant information and targets edge cases straddling the mathematical decision boundary.141 **Notable Insights & Ciekawostki** This methodology drastically reduces the overall financial footprint of data labeling, guaranteeing absolute maximum informational gain for every human interaction hour.141

## **Crowdsourcing**

Crowdsourcing distributes vast labeling tasks (like image bounding boxes or sentiment tagging) to a massive, decentralized pool of non-expert human workers. It leverages sheer volume and majority consensus algorithms to filter out human error and produce massive datasets rapidly.63 **Notable Insights & Ciekawostki** Without platforms like Amazon Mechanical Turk executing massive crowdsourced annotation, the foundational creation of ImageNet—and the subsequent deep learning revolution—would have been impossible.

## **Human-in-the-loop**

Human-in-the-loop (HITL) embeds human insight natively into a machine learning operation, bridging raw algorithmic capability with contextual, real-world judgment.142 It forms a safety spectrum spanning from "strict loop" oversight (where the AI solely recommends and humans decide) to "on the loop" (autonomous execution equipped with a human kill switch).143 **Notable Insights & Ciekawostki** High-stakes deployments like clinical oncology diagnosis and lethal autonomous weapons heavily depend on HITL architectures to mitigate life-threatening false positives and ensure strict ethical accountability.143 !([https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Human\_in\_the\_loop.png/800px-Human\_in\_the\_loop.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Human_in_the_loop.png/800px-Human_in_the_loop.png))

## **Mechanistic interpretability**

Mechanistic interpretability strictly seeks to reverse-engineer deep neural networks, translating alien matrices into human-comprehensible circuit logic.144 Researchers mathematically attempt to trace activation pathways to isolate single "features," actively attacking polysemanticity (where single neurons encode chaotic, overlapped meanings).146 **Notable Insights & Ciekawostki** It views neural models not merely as black-box appliances but as exotic virtual machines, providing an unprecedented causal understanding of exactly how an AI processes an output.32

## **RLHF**

Reinforcement Learning from Human Feedback (RLHF) solves the problem of defining strict mathematical functions for qualitative behaviors (e.g., "politeness") by training a separate "Reward Model" entirely based on human rankings.149 The main AI then utilizes Proximal Policy Optimization (PPO) to fiercely maximize its return against this newly constructed human-preference proxy.150 **Notable Insights & Ciekawostki** While RLHF enables AI to feel conversational and highly responsive, poor curation of the human preference data injects insidious, highly magnified societal biases into the final model.150 !(https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/RLHF\_pipeline.png/800px-RLHF\_pipeline.png)

## ---

**Model diagnostics**

Diagnostics provide the rigorous statistical verification required to quantify a model's true real-world efficacy, mathematically validating boundary limitations before deployment.

## **Coefficient of determination**

The Coefficient of determination () calculates the exact proportion of the variance in the dependent variable that is mathematically predictable from the model's independent inputs. Evaluated as , it scales exactly between 0 (complete noise) and 1 (perfect geometric fit).

**Notable Insights & Ciekawostki**

A common mathematical trap is that aggressively adding junk variables to a model artificially inflates standard , necessitating the use of the "Adjusted " metric to explicitly penalize complexity.

!(https://upload.wikimedia.org/wikipedia/commons/thumb/8/86/Coefficient\_of\_Determination.svg/800px-Coefficient\_of\_Determination.svg.png)

## **Confusion matrix**

A confusion matrix is a rigid tabular summary displaying absolute counts of true positives, true negatives, false positives (Type I error), and false negatives (Type II error). It forms the mathematical bedrock for deriving highly specific diagnostic metrics like Precision, Recall, and F1-Score.

**Notable Insights & Ciekawostki**

Analyzing the raw matrix immediately exposes imbalanced datasets—situations where a model achieves "99% accuracy" purely by guessing the dominant class on every single attempt.

## **Learning curve**

Learning curves map diagnostic performance (e.g., cross-entropy loss) on both the training and validation datasets over the continuous axis of training epochs or data volume. They vividly plot the convergence trajectory of an algorithm, visually isolating moments where learning mathematically stalls or diverges.

**Notable Insights & Ciekawostki**

An expanding gap between the training curve and the validation curve visually confirms severe overfitting, alerting the practitioner to halt training instantly.

## **ROC curve**

The Receiver Operating Characteristic (ROC) curve maps the True Positive Rate against the False Positive Rate across all possible mathematical classification thresholds. The absolute quality of the model is summarized by the Area Under the Curve (AUC), where 0.5 designates pure random guessing and 1.0 represents perfect discrimination.

**Notable Insights & Ciekawostki**

It elegantly divorces a model's innate mathematical discriminatory power from the subjective, user-defined boundary cutoff line.

!([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/ROC\_space.png/800px-ROC\_space.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/ROC_space.png/800px-ROC_space.png))

## ---

**Mathematical foundations**

The theoretical mechanics of machine learning rely entirely on bounded probability limits, risk minimization matrices, and computational complexity physics.

## **Kernel machines**

Kernel machines implicitly project data inputs into infinite-dimensional spaces using metrics like Gaussian radial basis functions to bypass severe non-linear limitations.74 The "Kernel Trick" radically reduces computational overhead by merely computing the inner product of vectors in the elevated space without ever explicitly performing the dimension mapping.74 **Notable Insights & Ciekawostki** By transforming intractable non-linear boundaries into linear hyperplanes in a higher dimension, kernel methods provide mathematically guaranteed global optima that neural networks simply cannot.74 !([https://upload.wikimedia.org/wikipedia/commons/thumb/f/f9/Kernel\_Machine.svg/800px-Kernel\_Machine.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f9/Kernel_Machine.svg/800px-Kernel_Machine.svg.png))

## **Bias–variance tradeoff**

The Bias-Variance tradeoff defines the fundamental mathematical conflict in predictive modeling: underfitting creates immense bias (ignoring reality), while overfitting creates extreme variance (memorizing noise). Decreasing bias inevitably increases variance, demanding optimization metrics to find the absolute center of generalization.

**Notable Insights & Ciekawostki**

Modern over-parameterized deep learning architectures exhibit a "double descent" phenomenon, where variance inexplicably drops after extreme overfitting, temporarily breaking classical statistical theory.

!([https://upload.wikimedia.org/wikipedia/commons/thumb/f/f9/Bias\_and\_variance\_contributing\_to\_total\_error.svg/800px-Bias\_and\_variance\_contributing\_to\_total\_error.svg.png](https://upload.wikimedia.org/wikipedia/commons/thumb/f/f9/Bias_and_variance_contributing_to_total_error.svg/800px-Bias_and_variance_contributing_to_total_error.svg.png))

## **Computational learning theory**

Computational Learning Theory (CLT) applies the rigorous concepts of computational complexity to machine learning, formulating precise bounds on the time, space, and data volume required to learn specific functions.151 It evaluates whether an algorithm can guarantee a result in polynomial time rather than brute-force exponential time.152 **Notable Insights & Ciekawostki** CLT bridges theoretical computer science with statistics, definitively separating learning problems that are mathematically "easy" from those that are fundamentally intractable.152 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Computational\_learning\_theory.png/800px-Computational\_learning\_theory.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Computational_learning_theory.png/800px-Computational_learning_theory.png))

## **Empirical risk minimization**

Empirical Risk Minimization (ERM) operates on the principle of minimizing the loss function (e.g., MSE) applied directly to the historical sample dataset.27 The objective is strictly to find a hypothesis that mathematically matches the given data set well as measured by the loss.153 **Notable Insights & Ciekawostki** While ERM is the core training step of modern machine learning libraries, true learning requires ensuring that minimizing empirical risk successfully maps to minimizing actual risk on unseen data.153 !([https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Empirical\_Risk\_Minimization.png/800px-Empirical\_Risk\_Minimization.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Empirical_Risk_Minimization.png/800px-Empirical_Risk_Minimization.png))

## **Occam learning**

Occam learning legally restricts the ERM process, enforcing the philosophical and mathematical law that simpler models mapping equivalent accuracy must unconditionally be preferred.155 This preference is modeled mathematically through the addition of a regularization term (like  penalty) in the learning problem.157 **Notable Insights & Ciekawostki** While simplicity serves truth approximation, overly simple model constraints dramatically increase the minimal data sample required to yield a probabilistic guarantee, briefly slowing the overall learning phase.157

## **PAC learning**

Probably Approximately Correct (PAC) learning defines the strict statistical probability that a randomly drawn hypothesis will maintain low generalization error bounds.151 The framework demands that the learner must be able to secure an efficient function with high probability (the "probably" part) possessing low error (the "approximately correct" part) using polynomial time and data.151 **Notable Insights & Ciekawostki** PAC effectively quantifies the computational difficulty of a task, laying the groundwork for identifying which classes of algorithms are guaranteed to succeed given infinite compute.151

## **Statistical learning theory**

Statistical Learning Theory (SLT) provides the overarching mathematical and statistical framework for describing how models generalize from finite samples.156 It unifies PAC learning, VC dimension, and ERM to form rigorous mathematical proofs regarding the reliability of inductive inference.159 **Notable Insights & Ciekawostki** Curiously, SLT remains distinct from classical frequentist and Bayesian statistics, offering a modernized philosophical justification for why algorithms can trust patterns seen in finite noise.159 !([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Statistical\_learning\_theory.png/800px-Statistical\_learning\_theory.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Statistical_learning_theory.png/800px-Statistical_learning_theory.png))

## **VC theory**

Vapnik–Chervonenkis (VC) theory measures a model's absolute shattering capacity—the maximum number of data points an algorithm can flawlessly separate regardless of geometric configuration.27 It relies on the shatter coefficient to map the effective size and complexity of a hypothesis class.27 **Notable Insights & Ciekawostki** VC dimension effectively dictates the computational capacity limit of an algorithm; infinite VC dimension implies the network can memorize anything but guarantees absolutely nothing regarding real-world generalization.27 !([https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/VC\_dimension\_shattering.png/800px-VC\_dimension\_shattering.png](https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/VC_dimension_shattering.png/800px-VC_dimension_shattering.png))

## **Topological deep learning**

Topological Deep Learning (TDL) harnesses geometric topology—utilizing simplicial complexes, cell complexes, and hypergraphs—to detect multi-scale shape invariants that standard grid-networks fail to perceive.160 Unlike simple graph node-edge connections, higher-order combinatorial cells allow networks to effortlessly learn non-linear group dynamics.161 **Notable Insights & Ciekawostki** TDL bypasses traditional statistical representations, capturing invariant properties embedded inside chaotic knot networks and biological curves that previously defied conventional mathematical description.161 !(https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Simplicial\_complex.svg/800px-Simplicial\_complex.svg.png)

## ---

**Journals and conferences**

The relentless acceleration of global machine learning relies almost exclusively on the fast-paced peer review of high-impact technical venues rather than traditional, sluggish journal distributions.

| Prestige Leader | Focus | Acceptance Rate | h5-index | Format |
| :---- | :---- | :---- | :---- | :---- |
| **NeurIPS** | Core ML, Deep Learning | 15-25% | > 300 | Conference (Fast, strict limits) |
| **ICML** | Core ML | 15-25% | > 260 | Conference (Fast, strict limits) |
| **JMLR** | ML Theory & Math | Highly Selective | 117 | Journal (Open Access, thorough) |
| **TPAMI** | Computer Vision | Selective | Highest in CV | Journal (Lengthy review) |

## **AAAI**

The Association for the Advancement of Artificial Intelligence (AAAI) is a foundational, broad-scope conference covering overarching AI subfields, extending deeply into logic and behavioral systems.164 **Notable Insights & Ciekawostki** It acts as a central historical pillar of the field, actively linking modern empirical deep learning researchers with classical symbolic and logic-based theoreticians.

## **ECML PKDD**

The European Conference on Machine Learning and Principles and Practice of Knowledge Discovery in Databases (ECML PKDD) is the premier European venue integrating fundamental machine learning with massive-scale data mining.

**Notable Insights & Ciekawostki**

It heavily emphasizes practical knowledge discovery, making it highly attractive for industrial applications intersecting with statistical theory.

!([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/ECML\_PKDD\_logo.png/800px-ECML\_PKDD\_logo.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/ECML_PKDD_logo.png/800px-ECML_PKDD_logo.png))

## **NeurIPS**

Neural Information Processing Systems (NeurIPS) dominates the modern AI academic tier, representing the absolute bleeding edge of representation learning, deep architecture proposals, and reinforcement breakthroughs.164 **Notable Insights & Ciekawostki** Unlike other scientific disciplines, an accepted paper at NeurIPS frequently holds significantly higher academic and commercial weight for tenure or employment than traditional high-impact journals.165 !(https://upload.wikimedia.org/wikipedia/commons/thumb/1/1b/Neurips\_logo.png/800px-Neurips\_logo.png)

## **ICML**

The International Conference on Machine Learning (ICML) is fiercely competitive, focusing intensely on algorithmic innovations, optimization techniques, and the core mathematics of learning theory.164 **Notable Insights & Ciekawostki** It is the principal battleground for proving new optimizer efficiencies and establishing foundational shifts in empirical risk limits.

## **ICLR**

The International Conference on Learning Representations (ICLR) focuses specifically on how data is structurally parsed and mapped inside neural networks.164 **Notable Insights & Ciekawostki** It pioneered the concept of Open Peer Review, making all reviewer comments and mathematical critiques completely public to foster ultimate transparency. !([https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/ICLR\_logo.png/800px-ICLR\_logo.png](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/ICLR_logo.png/800px-ICLR_logo.png))

## **IJCAI**

The International Joint Conference on Artificial Intelligence (IJCAI) matches AAAI in scope, covering wide-ranging topics from game theory and multi-agent systems to deep learning deployment.164 **Notable Insights & Ciekawostki** It remains the oldest ongoing AI conference, serving as a historical chronicle of AI's evolution since 1969.166

## **ML**

The *Machine Learning* (ML) journal maintains rigorous theoretical documentation, demanding deeply extensive mathematical proofs and ablation studies not suited for tight conference limits.167 **Notable Insights & Ciekawostki** While slower to publish, papers here form the permanent, undisputed mathematical canon of the field.

## **JMLR**

The Journal of Machine Learning Research (JMLR) is the premier journal strictly focused on the theoretical and mathematical underpinnings of machine learning.164 **Notable Insights & Ciekawostki** JMLR strictly operates as a platinum open-access journal, fundamentally enforcing the ethos that maximum global algorithmic progression requires uncompromising, free access to state-of-the-art methodology.164 !(https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/JMLR\_logo.png/800px-JMLR\_logo.png)

## ---

**Related articles**

Peripheral concepts and curated aggregations essential for contextual navigation of the discipline.

## **Glossary of artificial intelligence**

A master compilation of defined terms, acting as a Rosetta Stone for navigating the dense, heavily overlapping terminology spanning statistics, computer science, and neurobiology.

**Notable Insights & Ciekawostki**

It provides critical disambiguation, tracking how terms like "weights" and "bias" possess completely divergent meanings when crossing from classical statistics into deep learning.

## **List of datasets for machine-learning research**

A curated aggregation of benchmark datasets categorized by domain (e.g., NLP, tabular, audio). The performance metric of new algorithmic architectures is strictly validated against these static baselines.

**Notable Insights & Ciekawostki**

Historically, algorithmic breakthroughs do not occur in a vacuum; they follow immediately after the release of massive, high-quality open-source datasets.

!([https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Dataset\_visualization.png/800px-Dataset\_visualization.png](https://upload.wikimedia.org/wikipedia/commons/thumb/3/36/Dataset_visualization.png/800px-Dataset_visualization.png))

#### **List of datasets in computer vision and image processing**

A critical sub-list featuring pixel-based datasets, spanning rudimentary benchmarks like MNIST to highly complex semantic segmentations like COCO and ImageNet. **Notable Insights & Ciekawostki** The creation of ImageNet in 2009 by Fei-Fei Li was the single catalyst that ignited the deep learning revolution, proving that data volume was the missing key to neural network viability.122 !([https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/ImageNet\_samples.png/800px-ImageNet\_samples.png](https://upload.wikimedia.org/wikipedia/commons/thumb/2/2a/ImageNet_samples.png/800px-ImageNet_samples.png))

## **Outline of machine learning**

A structural taxonomy mapping the interconnected sub-disciplines, theorems, and mathematical optimizations that physically comprise the field.

**Notable Insights & Ciekawostki**

It acts as the ultimate roadmap for practitioners, visually proving that modern machine learning is not a monolith, but a delicate synthesis of statistics, linear algebra, and software engineering.

#### **Works cited**

> 1. What is Machine Learning? | IBM, accessed on March 26, 2026, [https://www.ibm.com/think/topics/machine-learning](https://www.ibm.com/think/topics/machine-learning)  
> 2. Machine Learning Paradigms - Introduction to Machine Learning - Wolfram, accessed on March 26, 2026, [https://www.wolfram.com/language/introduction-machine-learning/machine-learning-paradigms/](https://www.wolfram.com/language/introduction-machine-learning/machine-learning-paradigms/)  
> 3. Machine Learning Paradigms: A Comprehensive Overview | by Prasan N H - Medium, accessed on March 26, 2026, [https://medium.com/@prasanNH/machine-learning-paradigms-a-comprehensive-overview-55498ff73eff](https://medium.com/@prasanNH/machine-learning-paradigms-a-comprehensive-overview-55498ff73eff)  
> 4. Introduction to Reinforcement Learning: Temporal Difference, SARSA, Q-Learning, accessed on March 26, 2026, [https://towardsdatascience.com/introduction-to-reinforcement-learning-temporal-difference-sarsa-q-learning-e8f22669c366/](https://towardsdatascience.com/introduction-to-reinforcement-learning-temporal-difference-sarsa-q-learning-e8f22669c366/)  
> 5. What Is Meta Learning? - IBM, accessed on March 26, 2026, [https://www.ibm.com/think/topics/meta-learning](https://www.ibm.com/think/topics/meta-learning)  
> 6. What it is meta-learning and how it can benefit your pupils - Maths — No Problem!, accessed on March 26, 2026, [https://mathsnoproblem.com/blog/teaching-practice/how-pupils-benefit-from-meta-learning](https://mathsnoproblem.com/blog/teaching-practice/how-pupils-benefit-from-meta-learning)  
> 7. Real Life Meta-Learning: Teaching and Learning to Learn | Towards Data Science, accessed on March 26, 2026, [https://towardsdatascience.com/real-life-meta-learning-teaching-and-learning-to-learn-186877376709/](https://towardsdatascience.com/real-life-meta-learning-teaching-and-learning-to-learn-186877376709/)  
> 8. Curriculum learning in reinforcement learning - The University of Texas at Austin, accessed on March 26, 2026, [https://repositories.lib.utexas.edu/items/d6530e2e-35e5-452e-972c-8b3bb1dea93b](https://repositories.lib.utexas.edu/items/d6530e2e-35e5-452e-972c-8b3bb1dea93b)  
> 9. Curriculum Learning for Reinforcement Learning Domains: A Framework and Survey - Journal of Machine Learning Research, accessed on March 26, 2026, [https://jmlr.org/papers/volume21/20-212/20-212.pdf](https://jmlr.org/papers/volume21/20-212/20-212.pdf)  
> 10. Curriculum learning. The concept of curriculum learning in… | by Noha Nekamiche | AIGuys, accessed on March 26, 2026, [https://medium.com/aiguys/curriculum-learning-83b1b2221f33](https://medium.com/aiguys/curriculum-learning-83b1b2221f33)  
> 11. CURRICULUM EFFECTS AND COMPOSITIONALITY EMERGE WITH IN-CONTEXT LEARNING IN NEURAL NETWORKS - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC10896373/](https://pmc.ncbi.nlm.nih.gov/articles/PMC10896373/)  
> 12. Rule-based machine learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Rule-based\_machine\_learning](https://en.wikipedia.org/wiki/Rule-based_machine_learning)  
> 13. Comparison of rule induction, decision trees and formal concept analysis approaches for classification - ResearchGate, accessed on March 26, 2026, [https://www.researchgate.net/publication/325276642\_Comparison\_of\_rule\_induction\_decision\_trees\_and\_formal\_concept\_analysis\_approaches\_for\_classification](https://www.researchgate.net/publication/325276642_Comparison_of_rule_induction_decision_trees_and_formal_concept_analysis_approaches_for_classification)  
> 14. Neuro-symbolic AI - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Neuro-symbolic\_AI](https://en.wikipedia.org/wiki/Neuro-symbolic_AI)  
> 15. Neuro-Symbolic AI for Advanced Signal and Image Processing: A Review of Recent Trends and Future Directions, accessed on March 26, 2026, [https://ieeexplore.ieee.org/iel8/6287639/10820123/11124915.pdf](https://ieeexplore.ieee.org/iel8/6287639/10820123/11124915.pdf)  
> 16. Unlocking the Potential of Generative AI through Neuro-Symbolic Architectures – Benefits and Limitations - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2502.11269v1](https://arxiv.org/html/2502.11269v1)  
> 17. Resistive Switching Random-Access Memory (RRAM): Applications and Requirements for Memory and Computing | Chemical Reviews - ACS Publications, accessed on March 26, 2026, [https://pubs.acs.org/doi/10.1021/acs.chemrev.4c00845](https://pubs.acs.org/doi/10.1021/acs.chemrev.4c00845)  
> 18. Electrochemical RAM - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Electrochemical\_RAM](https://en.wikipedia.org/wiki/Electrochemical_RAM)  
> 19. Electrochemical Random Access Memory (ECRAM): State of the Art | by Copperpod IP, accessed on March 26, 2026, [https://copperpod.medium.com/electrochemical-random-access-memory-ecram-state-of-the-art-90c9f60263ab](https://copperpod.medium.com/electrochemical-random-access-memory-ecram-state-of-the-art-90c9f60263ab)  
> 20. Quantum Machine Learning 101: Beginner's Guide to Big Data - BlueQubit, accessed on March 26, 2026, [https://www.bluequbit.io/quantum-machine-learning](https://www.bluequbit.io/quantum-machine-learning)  
> 21. Quantum machine learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Quantum\_machine\_learning](https://en.wikipedia.org/wiki/Quantum_machine_learning)  
> 22. Quantum Machine Learning Explained - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=NqHKr9CGWJ0](https://www.youtube.com/watch?v=NqHKr9CGWJ0)  
> 23. All Machine Learning Models Clearly Explained! - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=0YdpwSYMY6I](https://www.youtube.com/watch?v=0YdpwSYMY6I)  
> 24. Machine Learning Cheat Sheet | DataCamp, accessed on March 26, 2026, [https://www.datacamp.com/cheat-sheet/machine-learning-cheat-sheet](https://www.datacamp.com/cheat-sheet/machine-learning-cheat-sheet)  
> 25. Diffusion model - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Diffusion\_model](https://en.wikipedia.org/wiki/Diffusion_model)  
> 26. Diffusion Models - David's Wiki, accessed on March 26, 2026, [https://wiki.davidl.me/view/Diffusion\_Models](https://wiki.davidl.me/view/Diffusion_Models)  
> 27. Mathematical Foundations of Machine Learning - Robert Nowak - University of Wisconsin–Madison, accessed on March 26, 2026, [https://nowak.ece.wisc.edu/MFML.pdf](https://nowak.ece.wisc.edu/MFML.pdf)  
> 28. Understanding and Comparing Clustering and Dimension Reduction Techniques | CodeSignal Learn, accessed on March 26, 2026, [https://codesignal.com/learn/courses/intro-to-unsupervised-machine-learning/lessons/understanding-and-comparing-clustering-and-dimension-reduction-techniques](https://codesignal.com/learn/courses/intro-to-unsupervised-machine-learning/lessons/understanding-and-comparing-clustering-and-dimension-reduction-techniques)  
> 29. Fuzzy clustering - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Fuzzy\_clustering](https://en.wikipedia.org/wiki/Fuzzy_clustering)  
> 30. T-distributed stochastic neighbor embedding - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/T-distributed\_stochastic\_neighbor\_embedding](https://en.wikipedia.org/wiki/T-distributed_stochastic_neighbor_embedding)  
> 31. Difference Between Factor Analysis and Principal Component Analysis - GeeksforGeeks, accessed on March 26, 2026, [https://www.geeksforgeeks.org/machine-learning/difference-between-factor-analysis-and-principal-component-analysis/](https://www.geeksforgeeks.org/machine-learning/difference-between-factor-analysis-and-principal-component-analysis/)  
> 32. Mechanistic Interpretability, Variables, and the Importance of Interpretable Bases, accessed on March 26, 2026, [https://www.transformer-circuits.pub/2022/mech-interp-essay](https://www.transformer-circuits.pub/2022/mech-interp-essay)  
> 33. Kernel Density Estimation explained step by step - Towards Data Science, accessed on March 26, 2026, [https://towardsdatascience.com/kernel-density-estimation-explained-step-by-step-7cc5b5bc4517/](https://towardsdatascience.com/kernel-density-estimation-explained-step-by-step-7cc5b5bc4517/)  
> 34. Anomaly detection using Isolation Forest and Local Outlier Factor | by Lekha Priya - Medium, accessed on March 26, 2026, [https://lekha-bhan88.medium.com/anomaly-detection-using-isolation-forest-and-local-outlier-factor-158985d6a45](https://lekha-bhan88.medium.com/anomaly-detection-using-isolation-forest-and-local-outlier-factor-158985d6a45)  
> 35. IsolationForest — scikit-learn 1.8.0 documentation, accessed on March 26, 2026, [https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.IsolationForest.html](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.IsolationForest.html)  
> 36. Local Outlier Factor - GeeksforGeeks, accessed on March 26, 2026, [https://www.geeksforgeeks.org/machine-learning/local-outlier-factor/](https://www.geeksforgeeks.org/machine-learning/local-outlier-factor/)  
> 37. How to perform anomaly detection with the LOF Algorithm - Towards Data Science, accessed on March 26, 2026, [https://towardsdatascience.com/how-to-perform-anomaly-detection-with-the-lof-algorithm-4fc31cee14c4/](https://towardsdatascience.com/how-to-perform-anomaly-detection-with-the-lof-algorithm-4fc31cee14c4/)  
> 38. Comprehensive review of dimensionality reduction algorithms: challenges, limitations, and innovative solutions - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC12453773/](https://pmc.ncbi.nlm.nih.gov/articles/PMC12453773/)  
> 39. Association rule learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Association\_rule\_learning](https://en.wikipedia.org/wiki/Association_rule_learning)  
> 40. What is latent semantic analysis? | IBM, accessed on March 26, 2026, [https://www.ibm.com/think/topics/latent-semantic-analysis](https://www.ibm.com/think/topics/latent-semantic-analysis)  
> 41. Topic Modeling with LSA, PSLA, LDA & lda2Vec | NanoNets - Medium, accessed on March 26, 2026, [https://medium.com/nanonets/topic-modeling-with-lsa-psla-lda-and-lda2vec-555ff65b0b05](https://medium.com/nanonets/topic-modeling-with-lsa-psla-lda-and-lda2vec-555ff65b0b05)  
> 42. Latent Semantic Analysis - GeeksforGeeks, accessed on March 26, 2026, [https://www.geeksforgeeks.org/machine-learning/latent-semantic-analysis/](https://www.geeksforgeeks.org/machine-learning/latent-semantic-analysis/)  
> 43. Boosted Bayesian Network Classifiers - Google Research, accessed on March 26, 2026, [https://research.google.com/pubs/archive/34636.pdf](https://research.google.com/pubs/archive/34636.pdf)  
> 44. Hidden Markov Models vs. Conditional Random Fields | Baeldung on Computer Science, accessed on March 26, 2026, [https://www.baeldung.com/cs/hidden-markov-vs-crf](https://www.baeldung.com/cs/hidden-markov-vs-crf)  
> 45. HMM, MEMM, and CRF: A Comparative Analysis of Statistical Modeling Methods, accessed on March 26, 2026, [https://www.alibabacloud.com/blog/hmm-memm-and-crf-a-comparative-analysis-of-statistical-modeling-methods\_592049](https://www.alibabacloud.com/blog/hmm-memm-and-crf-a-comparative-analysis-of-statistical-modeling-methods_592049)  
> 46. A Short Introduction to Learning to Rank - Northeastern University, accessed on March 26, 2026, [https://course.khoury.northeastern.edu/cs6200s14/ssl/data/short\_intro.pdf](https://course.khoury.northeastern.edu/cs6200s14/ssl/data/short_intro.pdf)  
> 47. Learning to rank - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Learning\_to\_rank](https://en.wikipedia.org/wiki/Learning_to_rank)  
> 48. Feature Extraction Techniques: PCA, LDA and t-SNE | by Ashwin N | Analytics Vidhya, accessed on March 26, 2026, [https://medium.com/analytics-vidhya/feature-extraction-techniques-pca-lda-and-t-sne-df0459c723aa](https://medium.com/analytics-vidhya/feature-extraction-techniques-pca-lda-and-t-sne-df0459c723aa)  
> 49. Restricted Boltzmann machine - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Restricted\_Boltzmann\_machine](https://en.wikipedia.org/wiki/Restricted_Boltzmann_machine)  
> 50. Restricted Boltzmann Machine - GeeksforGeeks, accessed on March 26, 2026, [https://www.geeksforgeeks.org/machine-learning/restricted-boltzmann-machine/](https://www.geeksforgeeks.org/machine-learning/restricted-boltzmann-machine/)  
> 51. A Beginner's Guide to Restricted Boltzmann Machines (RBMs) | Pathmind, accessed on March 26, 2026, [http://wiki.pathmind.com/restricted-boltzmann-machine](http://wiki.pathmind.com/restricted-boltzmann-machine)  
> 52. Learning to Rank, accessed on March 26, 2026, [https://netman.aiops.org/~peidan/ANM2021/2.MachineLearningBasics/LectureCoverage/31.LearningToRank.pdf](https://netman.aiops.org/~peidan/ANM2021/2.MachineLearningBasics/LectureCoverage/31.LearningToRank.pdf)  
> 53. Learning to Rank: A Complete Guide to Ranking using Machine Learning | by Francesco Casalegno | TDS Archive | Medium, accessed on March 26, 2026, [https://medium.com/data-science/learning-to-rank-a-complete-guide-to-ranking-using-machine-learning-4c9688d370d4](https://medium.com/data-science/learning-to-rank-a-complete-guide-to-ranking-using-machine-learning-4c9688d370d4)  
> 54. Learning to Rank in Structured Prediction Models - NAVER LABS Europe, accessed on March 26, 2026, [https://europe.naverlabs.com/research/publications/learning-to-rank-in-structured-prediction-models/](https://europe.naverlabs.com/research/publications/learning-to-rank-in-structured-prediction-models/)  
> 55. Grammar induction - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Grammar\_induction](https://en.wikipedia.org/wiki/Grammar_induction)  
> 56. Grammar induction - Bionity, accessed on March 26, 2026, [https://www.bionity.com/en/encyclopedia/Grammar\_Induction.html](https://www.bionity.com/en/encyclopedia/Grammar_Induction.html)  
> 57. Grammar Induction - The Stanford Natural Language Processing Group, accessed on March 26, 2026, [https://nlp.stanford.edu/projects/project-induction.shtml](https://nlp.stanford.edu/projects/project-induction.shtml)  
> 58. The “Ontology” in Machine Learning | by Xtreme1 | Multimodal Data Training | Medium, accessed on March 26, 2026, [https://medium.com/multisensory-data-training/the-ontology-in-machine-learning-e4ba59cd3fc4](https://medium.com/multisensory-data-training/the-ontology-in-machine-learning-e4ba59cd3fc4)  
> 59. Understanding DeepMind's Flamingo Visual Language Models | by Szymon Palucha, accessed on March 26, 2026, [https://medium.com/@paluchasz/understanding-flamingo-visual-language-models-bea5eeb05268](https://medium.com/@paluchasz/understanding-flamingo-visual-language-models-bea5eeb05268)  
> 60. Introduction to Flamingo VLM: Understanding the architecture and running inference, accessed on March 26, 2026, [https://www.youtube.com/watch?v=dMgAUnOaf1w](https://www.youtube.com/watch?v=dMgAUnOaf1w)  
> 61. Flamingo: Visual Language Model for Few-Shot Learning - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=wkeA2oND-pg](https://www.youtube.com/watch?v=wkeA2oND-pg)  
> 62. What Is Random Forest? | IBM, accessed on March 26, 2026, [https://www.ibm.com/think/topics/random-forest](https://www.ibm.com/think/topics/random-forest)  
> 63. Mathematical Foundations of Supervised Learning - mediaTUM, accessed on March 26, 2026, [https://mediatum.ub.tum.de/doc/1723378/1723378.pdf](https://mediatum.ub.tum.de/doc/1723378/1723378.pdf)  
> 64. 7 Stories Behind the World's Most Popular Machine Learning Algorithms - Medium, accessed on March 26, 2026, [https://medium.com/bcggamma/7-stories-behind-the-worlds-most-popular-machine-learning-algorithms-51472939d14b](https://medium.com/bcggamma/7-stories-behind-the-worlds-most-popular-machine-learning-algorithms-51472939d14b)  
> 65. Lecture 9: Density Estimation — Applied ML, accessed on March 26, 2026, [https://kuleshov-group.github.io/aml-book/contents/lecture9-density-estimation.html](https://kuleshov-group.github.io/aml-book/contents/lecture9-density-estimation.html)  
> 66. 5 Popular CNN Architectures Clearly Explained and Visualized - Towards Data Science, accessed on March 26, 2026, [https://towardsdatascience.com/5-most-well-known-cnn-architectures-visualized-af76f1f0065e/](https://towardsdatascience.com/5-most-well-known-cnn-architectures-visualized-af76f1f0065e/)  
> 67. Category:Artificial neural networks - Wikimedia Commons, accessed on March 26, 2026, [https://commons.wikimedia.org/wiki/Category:Artificial\_neural\_networks](https://commons.wikimedia.org/wiki/Category:Artificial_neural_networks)  
> 68. Timeline of machine learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Timeline\_of\_machine\_learning](https://en.wikipedia.org/wiki/Timeline_of_machine_learning)  
> 69. Supervised Learning Cheatsheet - CS 229, accessed on March 26, 2026, [https://stanford.edu/~shervine/teaching/cs-229/cheatsheet-supervised-learning/](https://stanford.edu/~shervine/teaching/cs-229/cheatsheet-supervised-learning/)  
> 70. The History of Artificial Intelligence - IBM, accessed on March 26, 2026, [https://www.ibm.com/think/topics/history-of-artificial-intelligence](https://www.ibm.com/think/topics/history-of-artificial-intelligence)  
> 71. Sparse Bayesian Learning and the Relevance Vector Machine, accessed on March 26, 2026, [https://www.jmlr.org/papers/volume1/tipping01a/tipping01a.pdf](https://www.jmlr.org/papers/volume1/tipping01a/tipping01a.pdf)  
> 72. Relevance vector machine - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Relevance\_vector\_machine](https://en.wikipedia.org/wiki/Relevance_vector_machine)  
> 73. The Relevance Vector Machine, accessed on March 26, 2026, [https://proceedings.neurips.cc/paper/1719-the-relevance-vector-machine.pdf](https://proceedings.neurips.cc/paper/1719-the-relevance-vector-machine.pdf)  
> 74. Support vector machine - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Support\_vector\_machine](https://en.wikipedia.org/wiki/Support_vector_machine)  
> 75. Support Vector Machines. Introduction to margins of separation… | by skilltohire - Medium, accessed on March 26, 2026, [https://medium.com/@skilltohire/support-vector-machines-4d28a427ebd](https://medium.com/@skilltohire/support-vector-machines-4d28a427ebd)  
> 76. Support Vector Machines (and the Kernel Trick) - Columbia University, accessed on March 26, 2026, [http://www.columbia.edu/~mh2078/MachineLearningORFE/SVMs\_MasterSlides.pdf](http://www.columbia.edu/~mh2078/MachineLearningORFE/SVMs_MasterSlides.pdf)  
> 77. The BIRCH clustering algorithm explained - Medium, accessed on March 26, 2026, [https://medium.com/@noel.cs21/balanced-iterative-reducing-and-clustering-using-heirachies-birch-5680adffaa58](https://medium.com/@noel.cs21/balanced-iterative-reducing-and-clustering-using-heirachies-birch-5680adffaa58)  
> 78. A Systematic Literature Review on Identifying Patterns Using Unsupervised Clustering Algorithms: A Data Mining Perspective - MDPI, accessed on March 26, 2026, [https://www.mdpi.com/2073-8994/15/9/1679](https://www.mdpi.com/2073-8994/15/9/1679)  
> 79. Comprehensive analysis of clustering algorithms: exploring limitations and innovative solutions - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC11419652/](https://pmc.ncbi.nlm.nih.gov/articles/PMC11419652/)  
> 80. Comparison of Dimensionality Reduction and Clustering Methods for Single-Cell Transcriptomics Data | bioRxiv, accessed on March 26, 2026, [https://www.biorxiv.org/content/10.1101/2022.10.15.512334.full](https://www.biorxiv.org/content/10.1101/2022.10.15.512334.full)  
> 81. What is BIRCH Algorithm? Working Process, Implementation & Limitations - upGrad, accessed on March 26, 2026, [https://www.upgrad.com/blog/what-is-birch-algorithm/](https://www.upgrad.com/blog/what-is-birch-algorithm/)  
> 82. Machine Learning Algorithms Cheat Sheet - GeeksforGeeks, accessed on March 26, 2026, [https://www.geeksforgeeks.org/machine-learning/machine-learning-algorithms-cheat-sheet/](https://www.geeksforgeeks.org/machine-learning/machine-learning-algorithms-cheat-sheet/)  
> 83. Clustering and Dimensionality Reduction: Understanding the “Magic” Behind Machine Learning | Imperva, accessed on March 26, 2026, [https://www.imperva.com/blog/clustering-and-dimensionality-reduction-understanding-the-magic-behind-machine-learning/](https://www.imperva.com/blog/clustering-and-dimensionality-reduction-understanding-the-magic-behind-machine-learning/)  
> 84. Fuzzy C-Means Clustering: Comprehensive Guide 2024 | Zemith.com, accessed on March 26, 2026, [https://www.zemith.com/blog/fuzzy-c-means-clustering-comprehensive-guide-2024](https://www.zemith.com/blog/fuzzy-c-means-clustering-comprehensive-guide-2024)  
> 85. simple and fast method to determine the parameters for fuzzy c–means cluster analysis - Oxford Academic, accessed on March 26, 2026, [https://academic.oup.com/bioinformatics/article/26/22/2841/227572](https://academic.oup.com/bioinformatics/article/26/22/2841/227572)  
> 86. OPTICS algorithm - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/OPTICS\_algorithm](https://en.wikipedia.org/wiki/OPTICS_algorithm)  
> 87. What is the difference between PCA, FA and LDA? - ResearchGate, accessed on March 26, 2026, [https://www.researchgate.net/post/What\_is\_the\_difference\_between\_PCA\_FA\_and\_LDA](https://www.researchgate.net/post/What_is_the_difference_between_PCA_FA_and_LDA)  
> 88. Sparse linear discriminant analysis for multiview structured data - PMC - NIH, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC8906173/](https://pmc.ncbi.nlm.nih.gov/articles/PMC8906173/)  
> 89. PCA and LDA Comparison | CodeSignal Learn, accessed on March 26, 2026, [https://codesignal.com/learn/courses/linear-landscapes-of-dimensionality-reduction-1/lessons/pca-and-lda-comparison](https://codesignal.com/learn/courses/linear-landscapes-of-dimensionality-reduction-1/lessons/pca-and-lda-comparison)  
> 90. factor analysis - What is the difference between PCA and LDA? - Stats StackExchange, accessed on March 26, 2026, [https://stats.stackexchange.com/questions/467284/what-is-the-difference-between-pca-and-lda](https://stats.stackexchange.com/questions/467284/what-is-the-difference-between-pca-and-lda)  
> 91. Learning of Graph Compressed Dictionaries for Sparse Representation Classification - SciTePress, accessed on March 26, 2026, [https://www.scitepress.org/papers/2016/57104/57104.pdf](https://www.scitepress.org/papers/2016/57104/57104.pdf)  
> 92. Proper Generalized Decomposition for Parametric Study and Material Distribution Design of Multi-Directional Functionally Graded Plates Based on 3D Elasticity Solution - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC8587020/](https://pmc.ncbi.nlm.nih.gov/articles/PMC8587020/)  
> 93. Proper generalized decomposition - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Proper\_generalized\_decomposition](https://en.wikipedia.org/wiki/Proper_generalized_decomposition)  
> 94. Application of Proper Generalized Decomposition to Multigroup Neutron Diffusion Eigenvalue Calculations - OSTI, accessed on March 26, 2026, [https://www.osti.gov/servlets/purl/1801174](https://www.osti.gov/servlets/purl/1801174)  
> 95. How t-SNE Outperforms PCA in Dimensionality Reduction | Towards Data Science, accessed on March 26, 2026, [https://towardsdatascience.com/how-t-sne-outperforms-pca-in-dimensionality-reduction-7a3975e8cbdb/](https://towardsdatascience.com/how-t-sne-outperforms-pca-in-dimensionality-reduction-7a3975e8cbdb/)  
> 96. Sparse dictionary learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Sparse\_dictionary\_learning](https://en.wikipedia.org/wiki/Sparse_dictionary_learning)  
> 97. Dictionary Learning Algorithms for Sparse Representation - PMC - NIH, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC2944020/](https://pmc.ncbi.nlm.nih.gov/articles/PMC2944020/)  
> 98. Lecture 6: Case Studies: HMM and CRF 1 Hidden Markov Models (HMMs), accessed on March 26, 2026, [https://www.cs.cmu.edu/~epxing/Class/10708-20/scribe/lec4\_scribe.pdf](https://www.cs.cmu.edu/~epxing/Class/10708-20/scribe/lec4_scribe.pdf)  
> 99. Exploring Conditional Random Fields for NLP Applications - Hyperscience, accessed on March 26, 2026, [https://www.hyperscience.ai/blog/exploring-conditional-random-fields-for-nlp-applications/](https://www.hyperscience.ai/blog/exploring-conditional-random-fields-for-nlp-applications/)  
> 100. Hidden Markov model - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Hidden\_Markov\_model](https://en.wikipedia.org/wiki/Hidden_Markov_model)  
> 101. Hidden Markov Models, accessed on March 26, 2026, [https://web.stanford.edu/~jurafsky/slp3/A.pdf](https://web.stanford.edu/~jurafsky/slp3/A.pdf)  
> 102. Viterbi algorithm - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Viterbi\_algorithm](https://en.wikipedia.org/wiki/Viterbi_algorithm)  
> 103. RANSAC - MATLAB & Simulink - MathWorks, accessed on March 26, 2026, [https://la.mathworks.com/discovery/ransac.html](https://la.mathworks.com/discovery/ransac.html)  
> 104. Random sample consensus - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Random\_sample\_consensus](https://en.wikipedia.org/wiki/Random_sample_consensus)  
> 105. Outlier detection using the RANSAC algorithm | by saurabh dasgupta - Medium, accessed on March 26, 2026, [https://medium.com/@saurabh.dasgupta1/outlier-detection-using-the-ransac-algorithm-de52670adb4a](https://medium.com/@saurabh.dasgupta1/outlier-detection-using-the-ransac-algorithm-de52670adb4a)  
> 106. Robust Homography Estimation with the RANSAC Algorithm - Purdue Engineering, accessed on March 26, 2026, [https://engineering.purdue.edu/kak/computervision/ECE661Folder/Lecture12.pdf](https://engineering.purdue.edu/kak/computervision/ECE661Folder/Lecture12.pdf)  
> 107. Foundations of Machine Learning - hlevkin, accessed on March 26, 2026, [https://www.hlevkin.com/hlevkin/45MachineDeepLearning/ML/Foundations\_of\_Machine\_Learning.pdf](https://www.hlevkin.com/hlevkin/45MachineDeepLearning/ML/Foundations_of_Machine_Learning.pdf)  
> 108. Types of Deep Neural Networks - MRI Questions, accessed on March 26, 2026, [https://mriquestions.com/deep-network-types.html](https://mriquestions.com/deep-network-types.html)  
> 109. Neural field - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Neural\_field](https://en.wikipedia.org/wiki/Neural_field)  
> 110. Six Types of Neural Networks You Need to Know About | SabrePC Blog, accessed on March 26, 2026, [https://www.sabrepc.com/blog/Deep-Learning-and-AI/6-types-of-neural-networks-to-know-about](https://www.sabrepc.com/blog/Deep-Learning-and-AI/6-types-of-neural-networks-to-know-about)  
> 111. CNN vs. RNN vs. LSTM vs. Transformer: A Comprehensive Comparison | by Emily Smith, accessed on March 26, 2026, [https://medium.com/@smith.emily2584/cnn-vs-rnn-vs-lstm-vs-transformer-a-comprehensive-comparison-b0eb9fdad4ce](https://medium.com/@smith.emily2584/cnn-vs-rnn-vs-lstm-vs-transformer-a-comprehensive-comparison-b0eb9fdad4ce)  
> 112. Illustrated Guide to LSTM's and GRU's: A step by step explanation - Medium, accessed on March 26, 2026, [https://medium.com/data-science/illustrated-guide-to-lstms-and-gru-s-a-step-by-step-explanation-44e9eb85bf21](https://medium.com/data-science/illustrated-guide-to-lstms-and-gru-s-a-step-by-step-explanation-44e9eb85bf21)  
> 113. Prediction of chaotic time series using recurrent neural networks and reservoir computing techniques: A comparative study - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC9230140/](https://pmc.ncbi.nlm.nih.gov/articles/PMC9230140/)  
> 114. Echo state network - Scholarpedia, accessed on March 26, 2026, [http://www.scholarpedia.org/article/Echo\_state\_network](http://www.scholarpedia.org/article/Echo_state_network)  
> 115. A practical guide to applying echo state networks - Bernoulli Institute for Mathematics, Computer Science and Artificial Intelligence, accessed on March 26, 2026, [https://www.ai.rug.nl/minds/uploads/PracticalESN.pdf](https://www.ai.rug.nl/minds/uploads/PracticalESN.pdf)  
> 116. Latent diffusion model - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Latent\_diffusion\_model](https://en.wikipedia.org/wiki/Latent_diffusion_model)  
> 117. Self-organizing map - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Self-organizing\_map](https://en.wikipedia.org/wiki/Self-organizing_map)  
> 118. Self-Organizing Map (SOM), accessed on March 26, 2026, [https://users.ics.aalto.fi/jhollmen/dippa/node9.html](https://users.ics.aalto.fi/jhollmen/dippa/node9.html)  
> 119. How Do Self Organizing Maps (SOMs) in Artificial Intelligence Learn? What Makes Them So POWERFUL? - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=XF1V8Af1i7c](https://www.youtube.com/watch?v=XF1V8Af1i7c)  
> 120. A Comparative Analysis of the Mamba, Transformer, and CNN Architectures for Multi-Label Chest X-Ray Anomaly Detection in the NIH ChestX-Ray14 Dataset - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC12428523/](https://pmc.ncbi.nlm.nih.gov/articles/PMC12428523/)  
> 121. U-Net - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/U-Net](https://en.wikipedia.org/wiki/U-Net)  
> 122. Top Historical Facts in Machine Learning - EnjoyAlgorithms, accessed on March 26, 2026, [https://www.enjoyalgorithms.com/blog/historical-facts-in-machine-learning/](https://www.enjoyalgorithms.com/blog/historical-facts-in-machine-learning/)  
> 123. What is NeRF? - Neural Radiance Fields Explained - AWS, accessed on March 26, 2026, [https://aws.amazon.com/what-is/neural-radiance-fields/](https://aws.amazon.com/what-is/neural-radiance-fields/)  
> 124. Neural radiance field - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Neural\_radiance\_field](https://en.wikipedia.org/wiki/Neural_radiance_field)  
> 125. Physics Informed Neural Networks (PINNs) \[Physics Informed Machine Learning\] - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=-zrY7P2dVC4](https://www.youtube.com/watch?v=-zrY7P2dVC4)  
> 126. What Are Physics-Informed Neural Networks (PINNs)? - MATLAB & Simulink - MathWorks, accessed on March 26, 2026, [https://www.mathworks.com/discovery/physics-informed-neural-networks.html](https://www.mathworks.com/discovery/physics-informed-neural-networks.html)  
> 127. Physics-Informed Neural Networks (PINNs) - The Synergy of Data & Physics in Deep Learning - Jousef Murad, accessed on March 26, 2026, [https://www.engineered-mind.com/engineering/physics-informed-neural-networks-pinns/](https://www.engineered-mind.com/engineering/physics-informed-neural-networks-pinns/)  
> 128. File:Full GPT architecture.svg - Wikimedia Commons, accessed on March 26, 2026, [https://commons.wikimedia.org/wiki/File:Full\_GPT\_architecture.svg](https://commons.wikimedia.org/wiki/File:Full_GPT_architecture.svg)  
> 129. SPMamba Architecture Overview - Emergent Mind, accessed on March 26, 2026, [https://www.emergentmind.com/topics/spmamba-architecture](https://www.emergentmind.com/topics/spmamba-architecture)  
> 130. \[2312.00752\] Mamba: Linear-Time Sequence Modeling with Selective State Spaces - arXiv, accessed on March 26, 2026, [https://arxiv.org/abs/2312.00752](https://arxiv.org/abs/2312.00752)  
> 131. Here Comes Mamba: The Selective State Space Model | Towards Data Science, accessed on March 26, 2026, [https://towardsdatascience.com/here-comes-mamba-the-selective-state-space-model-435e5d17a451/](https://towardsdatascience.com/here-comes-mamba-the-selective-state-space-model-435e5d17a451/)  
> 132. Differences between Q-learning and SARSA - GeeksforGeeks, accessed on March 26, 2026, [https://www.geeksforgeeks.org/artificial-intelligence/differences-between-q-learning-and-sarsa/](https://www.geeksforgeeks.org/artificial-intelligence/differences-between-q-learning-and-sarsa/)  
> 133. What is the difference between Q-learning and SARSA? - Stack Overflow, accessed on March 26, 2026, [https://stackoverflow.com/questions/6848828/what-is-the-difference-between-q-learning-and-sarsa](https://stackoverflow.com/questions/6848828/what-is-the-difference-between-q-learning-and-sarsa)  
> 134. RL4.1 Introduction: TD-methods versus Policy Gradients - YouTube, accessed on March 26, 2026, [https://www.youtube.com/watch?v=sp1qlnzmtps](https://www.youtube.com/watch?v=sp1qlnzmtps)  
> 135. RL — Reinforcement Learning Algorithms Comparison | by Jonathan Hui - Medium, accessed on March 26, 2026, [https://jonathan-hui.medium.com/rl-reinforcement-learning-algorithms-comparison-76df90f180cf](https://jonathan-hui.medium.com/rl-reinforcement-learning-algorithms-comparison-76df90f180cf)  
> 136. Reinforcement Learning: Temporal Difference (TD) Learning – Jordan J Hood, accessed on March 26, 2026, [https://www.lancaster.ac.uk/stor-i-student-sites/jordan-j-hood/2021/04/12/reinforcement-learning-temporal-difference-td-learning/](https://www.lancaster.ac.uk/stor-i-student-sites/jordan-j-hood/2021/04/12/reinforcement-learning-temporal-difference-td-learning/)  
> 137. Multi-agent reinforcement learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Multi-agent\_reinforcement\_learning](https://en.wikipedia.org/wiki/Multi-agent_reinforcement_learning)  
> 138. \[2408.01072\] A Survey on Self-play Methods in Reinforcement Learning - arXiv, accessed on March 26, 2026, [https://arxiv.org/abs/2408.01072](https://arxiv.org/abs/2408.01072)  
> 139. One Model, All Roles: Multi-Turn, Multi-Agent Self-Play Reinforcement Learning for Conversational Social Intelligence - Microsoft, accessed on March 26, 2026, [https://www.microsoft.com/en-us/research/publication/one-model-all-roles-multi-turn-multi-agent-self-play-reinforcement-learning-for-conversational-social-intelligence/](https://www.microsoft.com/en-us/research/publication/one-model-all-roles-multi-turn-multi-agent-self-play-reinforcement-learning-for-conversational-social-intelligence/)  
> 140. How Multi-Agent AI learn by continuously competing against themselves | Self Play, accessed on March 26, 2026, [https://www.youtube.com/watch?v=0WMvzpAYJGk](https://www.youtube.com/watch?v=0WMvzpAYJGk)  
> 141. Active Learning and Uncertainty Sampling - Keymakr, accessed on March 26, 2026, [https://keymakr.com/blog/uncertainty-sampling-explained/](https://keymakr.com/blog/uncertainty-sampling-explained/)  
> 142. What is Human-in-the-Loop (HITL) in AI & ML? - Google Cloud, accessed on March 26, 2026, [https://cloud.google.com/discover/human-in-the-loop](https://cloud.google.com/discover/human-in-the-loop)  
> 143. What is 🤝Human-in-the-Loop?The Spectrum of Human AI Interaction. | by Tahir | Mar, 2026, accessed on March 26, 2026, [https://medium.com/@tahirbalarabe2/what-is-human-in-the-loop-the-spectrum-of-human-ai-interaction-0f762426a094](https://medium.com/@tahirbalarabe2/what-is-human-in-the-loop-the-spectrum-of-human-ai-interaction-0f762426a094)  
> 144. Mechanistic Interpretability: Understanding the Inner Workings of AI Models - Medium, accessed on March 26, 2026, [https://medium.com/@MakeComputerScienceGreatAgain/mechanistic-interpretability-understanding-the-inner-workings-of-ai-models-399e21d5603c](https://medium.com/@MakeComputerScienceGreatAgain/mechanistic-interpretability-understanding-the-inner-workings-of-ai-models-399e21d5603c)  
> 145. Unboxing the Black Box: Mechanistic Interpretability for Algorithmic Understanding of Neural Networks - arXiv, accessed on March 26, 2026, [https://arxiv.org/html/2511.19265v1](https://arxiv.org/html/2511.19265v1)  
> 146. Towards Monosemanticity: Decomposing Language Models With Dictionary Learning, accessed on March 26, 2026, [https://transformer-circuits.pub/2023/monosemantic-features](https://transformer-circuits.pub/2023/monosemantic-features)  
> 147. Mechanistic Interpretability: A Challenge Common to Both Artificial and Biological Intelligence - Kempner Institute, accessed on March 26, 2026, [https://kempnerinstitute.harvard.edu/research/deeper-learning/mechanistic-interpretability-a-challenge/](https://kempnerinstitute.harvard.edu/research/deeper-learning/mechanistic-interpretability-a-challenge/)  
> 148. Mechanistic Interpretability | Decode Neural Networks - Cloud Security Alliance (CSA), accessed on March 26, 2026, [https://cloudsecurityalliance.org/blog/2024/09/05/mechanistic-interpretability-101](https://cloudsecurityalliance.org/blog/2024/09/05/mechanistic-interpretability-101)  
> 149. What is RLHF? - Reinforcement Learning from Human Feedback Explained - AWS, accessed on March 26, 2026, [https://aws.amazon.com/what-is/reinforcement-learning-from-human-feedback/](https://aws.amazon.com/what-is/reinforcement-learning-from-human-feedback/)  
> 150. Reinforcement learning from human feedback - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Reinforcement\_learning\_from\_human\_feedback](https://en.wikipedia.org/wiki/Reinforcement_learning_from_human_feedback)  
> 151. Probably approximately correct learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Probably\_approximately\_correct\_learning](https://en.wikipedia.org/wiki/Probably_approximately_correct_learning)  
> 152. Computational Learning Theory, accessed on March 26, 2026, [https://www.cs.ox.ac.uk/people/varun.kanade/teaching/CLT-MT2022/lectures/CLT.pdf](https://www.cs.ox.ac.uk/people/varun.kanade/teaching/CLT-MT2022/lectures/CLT.pdf)  
> 153. Empirical risk minimization is not learning : A mathematical definition of learning and re-understanding of overfitting and Occam's razor in machine learning - Scientific Memo, accessed on March 26, 2026, [http://science-memo.blogspot.com/2022/06/empirical-risk-minimisation-learning-curve.html](http://science-memo.blogspot.com/2022/06/empirical-risk-minimisation-learning-curve.html)  
> 154. Empirical Risk Minimization - Stanford University, accessed on March 26, 2026, [https://ee104.stanford.edu/lectures/erm.pdf](https://ee104.stanford.edu/lectures/erm.pdf)  
> 155. Ockham's Razor: the Basic Principle of Statistical Learning Theory - Duke University, accessed on March 26, 2026, [https://users.cs.duke.edu/~cynthia/CourseNotes/OckhamsRazorBasics.pdf](https://users.cs.duke.edu/~cynthia/CourseNotes/OckhamsRazorBasics.pdf)  
> 156. \[2312.13842\] Statistical learning theory and Occam's razor: The core argument - arXiv, accessed on March 26, 2026, [https://arxiv.org/abs/2312.13842](https://arxiv.org/abs/2312.13842)  
> 157. Simple Models in Complex Worlds: Occam's Razor and Statistical Learning Theory, accessed on March 26, 2026, [https://d-nb.info/1260359670/34](https://d-nb.info/1260359670/34)  
> 158. A Gentle Introduction to Computational Learning Theory - MachineLearningMastery.com, accessed on March 26, 2026, [https://machinelearningmastery.com/introduction-to-computational-learning-theory/](https://machinelearningmastery.com/introduction-to-computational-learning-theory/)  
> 159. statistical learning theory and occam's razor: the argument from empirical risk minimization - PhilSci-Archive, accessed on March 26, 2026, [http://philsci-archive.pitt.edu/22259/1/sltoccaerm.pdf](http://philsci-archive.pitt.edu/22259/1/sltoccaerm.pdf)  
> 160. Topological data analysis and topological deep learning beyond persistent homology: a review - PMC, accessed on March 26, 2026, [https://pmc.ncbi.nlm.nih.gov/articles/PMC12931839/](https://pmc.ncbi.nlm.nih.gov/articles/PMC12931839/)  
> 161. Topological deep learning - Wikipedia, accessed on March 26, 2026, [https://en.wikipedia.org/wiki/Topological\_deep\_learning](https://en.wikipedia.org/wiki/Topological_deep_learning)  
> 162. (PDF) Topological data analysis and topological deep learning beyond persistent homology: a review - ResearchGate, accessed on March 26, 2026, [https://www.researchgate.net/publication/398933147\_Topological\_data\_analysis\_and\_topological\_deep\_learning\_beyond\_persistent\_homology\_a\_review](https://www.researchgate.net/publication/398933147_Topological_data_analysis_and_topological_deep_learning_beyond_persistent_homology_a_review)  
> 163. A review of topological data analysis and topological deep learning in molecular sciences, accessed on March 26, 2026, [https://arxiv.org/html/2509.16877v1](https://arxiv.org/html/2509.16877v1)  
> 164. AI Machine Learning Journals: Top Publication Venues, accessed on March 26, 2026, [https://ijctjournal.org/ai-machine-learning-journals/](https://ijctjournal.org/ai-machine-learning-journals/)  
> 165. \[D\] ML Conferences and Organization Metrics : r/MachineLearning - Reddit, accessed on March 26, 2026, [https://www.reddit.com/r/MachineLearning/comments/1d4shqn/d\_ml\_conferences\_and\_organization\_metrics/](https://www.reddit.com/r/MachineLearning/comments/1d4shqn/d_ml_conferences_and_organization_metrics/)  
> 166. Artificial Intelligence - Google Scholar Metrics, accessed on March 26, 2026, [https://scholar.google.es/citations?view\_op=top\_venues\&hl=en\&vq=eng\_artificialintelligence](https://scholar.google.es/citations?view_op=top_venues&hl=en&vq=eng_artificialintelligence)  
> 167. Best Computer Science Journals for Machine Learning & Artificial intelligence, accessed on March 26, 2026, [https://research.com/journals-rankings/computer-science/machine-learning](https://research.com/journals-rankings/computer-science/machine-learning)
