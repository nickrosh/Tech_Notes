*Baselines* are simple benchmarks which pave the way for iterative model development:
- Rapid experimentation via hyperparameter tuning thanks to low model complexity
- Discovery of data issues, false assumptions, bugs in code, etc. Since the model itself is not complex
- Pareto's principle: we can achieve decent performance with minimal initial effort.

Say that your [Training Data](Training%20Data.md) is 90% in one class and 10% in another. If you simply make a model that randomly chooses the main class 90% of the time, your [F1](../ML%20Metrics.md) would be 0.9. This of  course, is not actually a useful model. Thus baselines are critical for determining if our models are actually performing well. Here is a high level approach to establishing baselines:
1. Start with the simples possible baseline to compare subsequent development with. This is often a random selection model.
2. Develop a rule-based approach (when possible) using IFTT, auxiliary data, etc.
3. Slowly add complexity by addressing limitations and motivating representations and model architectures.
4. Weigh tradeoffs (performance, latency, size, etc.) between performant baselines.
5. Revisit and iterate on baselines as your dataset grows.

The exact baseline should vary from one use case to another, but here are the five baselines that might be useful across use cases:


#### Random Baseline

If our model just predicts at random, what's the expected performance? The predictions are generated at random following a specific distribution, which can be the uniform distribution or the task's label distribution.

#### Simple Heuristic

Forget ML. If you just make predictions based on simple heuristics, what performance would you expect? For example, if you want to build a ranking system to rank items on a user's newsfeed with the goal of getting that user to spend more time on the newsfeed, how much time would a user spend if you just rank all the items in reverse chronological order, showing the latest one first?

#### Zero Rule Baseline

The zero rule baseline is a special case of the simple heuristic baseline when your baseline model always predicts the most common class. For example, for the task of recommending the app a user is most likely to use next on their phone, the simplest model would be to recommend their most frequently used app. If this simple heuristic can predict the next app accurately 70% of the time, any model you build has to outperform it significantly to justify the added complexity.

#### Human Baseline

In many cases, the goal of ML is to automate what would have been otherwise done by humans, so it's useful to know how your model performs compared to human experts. For example, if you work on a self-driving system, it's crucial to measure your system's progress compared to human drivers, because otherwise you might never be able to convince your users to trust this system. Even if your system isn't meant to replace human experts and only to add them in improving their productivity, it's still important to know in what scenarios this system would be useful to humans.

#### Existing Solutions

In many cases, ML systems are designed to replace existing solutions, which might be business logic with a lot of if/else statements or third-party solutions. It's crucial to compare your new model to these existing solutions. Your ML model doesn't always have to be better than existing solutions to be useful. A model whose performance is a little bit inferior can still be useful if it's much easier or cheaper to use.