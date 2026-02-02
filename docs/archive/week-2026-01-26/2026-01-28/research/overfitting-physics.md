# Overfitting and Generalization in Physics-Informed ML Models

| | |
|---|---|
| **Source** | r/MachineLearning |
| **URL** | [old.reddit.com/r/MachineLearning](https://old.reddit.com/r/MachineLearning/) |
| **Researched** | 2026-01-28 |

## Overview

Recent discussions on r/MachineLearning reveal a critical architectural challenge in physics-informed neural networks (PINNs) and physics surrogate models: achieving high test-set accuracy while maintaining physical consistency and generalization on out-of-distribution data. The core tension centers on memorization versus learning of underlying physical functions—particularly the interpolation-extrapolation gap in engineering applications like FEA surrogate modeling.

## Key Points

- **Interpolation vs. Extrapolation Problem**: Neural network surrogates achieve >95% R² on in-distribution test data while producing physically inconsistent predictions during sensitivity analyses (parameter sweeps). The model memorizes the training cloud geometry rather than learning the underlying physical relationships.

- **Data Cloud Memorization**: The fundamental issue is that standard neural networks optimize for point-wise error minimization without embedded physical constraints. This leads to failure modes where the model confidently predicts values that violate known physics trends and domain constraints.

- **Physics Constraints as Regularization**: The community consensus is that adding explicit physics-informed constraints (PINN approach) directly into the loss function serves as strong inductive bias, forcing the model to learn meaningful physical relationships rather than interpolating between training points.

- **Alternative Approaches**: Beyond PINNs, Gaussian Process regression (Kriging) and other Bayesian methods are mentioned as viable alternatives that provide uncertainty quantification and more conservative extrapolation behavior out-of-distribution.

- **Uncertainty Quantification Gap**: Discussion around Bayesian PINNs reveals ongoing research into which components (weights, data, boundary conditions, physical parameters) should be treated probabilistically for improved generalization and confidence calibration.

## Technical Details

### The Core Problem

A user reported training a neural network surrogate for FEA motor design simulations:
- Test R² > 0.95 on held-out data (strong apparent performance)
- Sensitivity analysis reveals inconsistency with known physics
- Predictions don't follow expected motor design trends

This is a classic manifestation of **high training/test accuracy with poor generalization to new regimes**—the model has essentially fit a high-dimensional interpolant through the training data distribution without capturing the underlying functional relationship.

### Architectural Solutions in Literature

**Physics-Informed Neural Networks (PINNs)**: Embed physical laws (PDEs, conservation laws) as soft constraints in the loss function:
```
Loss = Loss_data + λ * Loss_physics
```
This approach forces the learned function to be consistent with known governing equations, serving as a structural regularizer.

**Bayesian PINNs**: Extend standard PINNs with uncertainty quantification by treating network parameters probabilistically. Open questions remain about whether to place uncertainty only on weights or also on data, boundary conditions, and physical parameters—suggesting this is still an active research frontier.

**Related Methods**:
- Gaussian Processes / Kriging for smaller datasets with explicit uncertainty
- Neural ODEs / Neural PDEs for temporal/spatial systems
- Test-Time Training approaches that adapt weights during inference based on context

### Learning Resources

The community references key texts:
- Steven Brunton & Nathan Kutz: *Data-Driven Science and Engineering* (classical PDE methods foundation)
- Simon Prince: *Understanding Deep Learning* (modern deep learning theory with manifold/topology treatment)
- Patrick Kidger's thesis (Neural ODEs and differential equation integration)
- Izhikevich: *Dynamics of Spiking Neural Networks* (dynamical systems perspective)
- Flow matching / diffusion literature for ODE-based generative modeling connections

## Implications

### For Practitioners

1. **Surrogate Modeling Red Flag**: High test accuracy alone is insufficient validation for physics-informed models. Mandatory sensitivity analyses and physics consistency checks must precede production deployment.

2. **Loss Function Design is Critical**: The loss function architecture determines generalization behavior. Point-wise MSE is insufficient; physics constraints must be explicit.

3. **Out-of-Distribution Behavior Matters**: Standard cross-validation on i.i.d. test splits misses the true failure mode: extrapolation to new parameter regimes not covered by training data. Design validation datasets that probe parameter space boundaries.

4. **Uncertainty is Feature, Not Bug**: Bayesian approaches and probabilistic models provide calibrated confidence that can flag out-of-distribution predictions. This is architecturally preferable to point estimates for safety-critical applications (structural design, thermal analysis).

### For Architecture/Research Decisions

- **Hybrid Models**: Consider domain-model + NN residual correction approaches (as discussed for actuarial mortality modeling) where domain expertise provides strong structural bias and NN learns only residual corrections.

- **Regularization Strategy**: Physics constraints compete with data fit in the loss function. The balance (λ parameter) must be tuned empirically; there's no universal solution across problem classes.

- **Generalization Testing**: Sensitivity analyses (parameter sweeps along each dimension) and out-of-distribution projection onto known solution manifolds are essential validation steps before claiming generalization.

- **Computational Trade-offs**: PINNs add physics loss evaluation cost (often requires automatic differentiation through PDE evaluation). For large-scale problems, this can dominate wall-clock time compared to pure data-driven models.

## Related

- [r/MachineLearning: High Accuracy (R^2 > 0.95) on Test Data but poor generalization on unseen physics data. Overfitting?](https://www.reddit.com/r/MachineLearning/comments/1qovt9s/dhigh_accuracy_r2_095_on_test_data_but_poor/) - Original problem statement with FEA surrogate case study
- [Bayesian physics informed neural networks (PINNs) [R]](https://old.reddit.com/r/MachineLearning/comments/1qj610w/bayesian_physics_informed_neural_networks_pinns_r/) - Open research questions on uncertainty treatment in PINNs
- [What are the must-have books for graduate students/researchers... especially for Neural ODEs/PDEs/SDEs, and PINNs?](https://old.reddit.com/r/MachineLearning/comments/1qaudob/d_what_are_the_musthave_books_for_graduate/) - Community reading list and foundational resources
- [Data-Driven Science and Engineering - Brunton & Kutz](https://www.cambridge.org/highereducation/books/data-driven-science-and-engineering/E13D29FF012C4E5D9ACFD1327C29C7C7) - Classical PDE solver methods as context
- [DeepSeek: Conditional Memory via Scalable Lookup](https://github.com/deepseek-ai/Engram) - Emerging memory-augmented approaches for structured generalization
