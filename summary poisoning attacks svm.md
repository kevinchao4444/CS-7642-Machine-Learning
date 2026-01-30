# Summary: Poisoning Attacks against Support Vector Machines (Biggio et al.)

## Paper Information
- **Authors**: Battista Biggio, Blaine Nelson, Pavel Laskov
- **Conference**: ICML 2012
- **Topic**: Adversarial machine learning, security of SVMs

## Overview
This paper investigates poisoning attacks against Support Vector Machines (SVMs), where an adversary injects specially crafted training data to increase the SVM's test error. The work demonstrates that intelligent adversaries can predict and exploit how SVMs respond to malicious training inputs.

## Problem Statement
- Most learning algorithms assume training data comes from natural/well-behaved distributions
- This assumption fails in security-sensitive settings (spam detection, intrusion detection, fraud detection)
- Adversaries can manipulate training data to degrade classifier performance
- **Poisoning attack**: Causative attack where malicious points are injected into training data

## Key Contributions

### 1. Attack Method
- Uses **gradient ascent** to construct attack points that maximize validation error
- Gradient computed based on properties of SVM's optimal solution
- Can be **kernelized** - works in input space even for non-linear kernels
- Exploits smooth dependency of SVM solution on training data geometry

### 2. Mathematical Foundation
- Based on incremental SVM learning (Cauwenberghs & Poggio, 2001)
- Maintains optimal SVM solution through adiabatic updates
- Uses KKT conditions to predict SVM response to data perturbations
- Gradient depends only on kernel dot products → enables kernelization

### 3. Attack Algorithm
**Input**: Training data, validation data, attack class label, initial attack point, step size

**Process**:
1. Initialize attack point by cloning a point from attacked class and flipping its label
2. Iteratively:
   - Recompute SVM solution with attack point (using incremental SVM)
   - Compute gradient of validation error
   - Update attack point in gradient direction
3. Terminate when validation error change is below threshold

**Key insight**: Small gradient steps preserve structure of SVM solution (support vectors, error vectors, reserve points)

## Kernel Support
The method works with multiple kernels:
- **Linear kernel**: ∂K/∂u = t·xi
- **Polynomial kernel**: ∂K/∂u = d(xi·xc + R)^(d-1)·t·xi  
- **RBF kernel**: ∂K/∂u = K(xi,xc)·γ·t·(xi - xc)

For non-linear kernels, approximation used by substituting x_c^(p-1) for x_c^(p) when step size is small.

## Experimental Results

### Artificial 2D Gaussian Data
- Two classes with Gaussian distributions
- Training: 25 points per class; Validation: 500 points per class
- Gradient ascent reliably finds good local maxima of non-convex error surface
- Works for both linear and RBF kernels

### MNIST Handwritten Digits
- Three binary classification tasks: 7 vs. 1, 9 vs. 8, 4 vs. 0
- Training: 100 samples; Validation: 500 samples; Test: ~2000 per class
- Linear kernel, C = 1

**Results**:
- **Single attack point** increased error from 2-5% to 15-20%
- Attack "blurs" digit toward appearance of attacking class
  - 7 straightens to resemble 1
  - 9's lower segment rounds to mimic 8
  - 4 adds round noise to resemble 0
- Significantly more effective than random label flips
- **Multi-point attacks**: Error grows steadily with percentage of poisoned training data

## Attacker Assumptions
- Knows the learning algorithm (SVM)
- Can draw data from underlying distribution
- Knows training data (or uses surrogate from same distribution)
- Can inject points with chosen labels
- Worst-case analysis of attacker capabilities

## Limitations & Future Work

### Current Limitations
1. **Small step sizes**: Required to maintain SVM structure; many iterations needed
2. **Label control**: Assumes attacker controls labels of injected points
3. **Inverse feature mapping**: Direct mapping assumed between real-world data and input features

### Future Directions
1. **Larger steps**: Compute maximum step that preserves optimal solution structure
2. **Multi-point optimization**: Simultaneously optimize multiple attack points rather than sequential single-point attacks
3. **Subset selection**: Choose optimal starting points for attacks
4. **Side constraints**: Incorporate constraints to fool labeling oracles (e.g., human labelers)
5. **Complex feature mappings**: Handle non-smooth operations and normalizations (e.g., spam filtering)

## Practical Implications
- SVMs are vulnerable to poisoning attacks in adversarial environments
- Single malicious training point can significantly degrade performance
- Important for security-sensitive applications:
  - Spam detection
  - Intrusion detection
  - Malware detection
  - Fraud detection
- Need to design learning algorithms resistant to adversarial training data

## Key Takeaways
- First comprehensive poisoning attack against SVMs
- Demonstrates feasibility of gradient-based attacks in input space for kernel methods
- Even crude gradient ascent achieves large impact on classification accuracy
- Highlights need for adversarially robust learning algorithms
- Opens door for assessing impact of input space transformations on kernel functions
