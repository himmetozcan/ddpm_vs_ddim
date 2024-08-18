There are key assumptions and modifications involved in converting the DDPM process to the DDIM process. These assumptions and changes allow DDIM to achieve deterministic and efficient sampling while still producing high-quality results. Below are the main assumptions and conceptual shifts that underpin the conversion from DDPM to DDIM:

### 1. **Reinterpretation of the Reverse Process as an Ordinary Differential Equation (ODE):**
   - **DDPM Assumption**: In DDPM, the reverse process is a stochastic process that is modeled as a series of Markovian steps. Each step involves sampling from a Gaussian distribution, which makes the process inherently random.
   - **DDIM Assumption**: DDIM reinterprets this reverse process as an ODE instead of a stochastic process. The ODE formulation assumes that the reverse process can be described deterministically by integrating a differential equation that governs the noise removal process. This ODE-based interpretation allows for deterministic transitions between noise levels without requiring stochastic sampling.

### 2. **Non-Markovian Dependence:**
   - **DDPM Assumption**: The DDPM reverse process is Markovian, meaning each step depends only on the immediate previous step \( x_t \) and not on any other past steps.
   - **DDIM Assumption**: DDIM assumes a non-Markovian process, where the reverse process can be generalized to depend on multiple previous steps. This means the state at time \( t-1 \) can be directly computed from \( x_t \) with a dependency that implicitly considers the initial data distribution. This non-Markovian nature is critical to allowing larger "jumps" between time steps.

### 3. **Noise Prediction Assumption (Shared with DDPM):**
   - **Noise Prediction Consistency**: Both DDPM and DDIM rely on the neural network \( \epsilon_\theta(x_t, t) \) to predict the noise added at each step. This assumption means that the network must accurately predict the noise for the reverse process to work effectively. DDIM extends this by assuming the same noise prediction can be used deterministically across multiple steps.

### 4. **Choice of Variance (Control Over Stochasticity):**
   - **DDPM Assumption**: The reverse process in DDPM involves sampling from a Gaussian distribution, where the variance is predetermined by the diffusion schedule.
   - **DDIM Assumption**: DDIM introduces a parameter \( \eta \) to control the variance in the reverse process. When \( \eta = 0 \), the reverse process is fully deterministic, but \( \eta \) can be adjusted to introduce some stochasticity. The assumption here is that the variance can be explicitly controlled, allowing for a smooth transition between deterministic and stochastic sampling.

### 5. **Equivalence of the Forward Process:**
   - **Shared Assumption**: Both DDPM and DDIM assume that the forward process, which adds noise to the data, follows the same formulation. This ensures that the noisy data \( x_t \) at any timestep \( t \) can be described using the same distribution \( q(x_t | x_0) \). The forward process is identical in both models and serves as the foundation for defining the reverse process.

### 6. **Modeling the Latent Variables (Jumping Between Timesteps):**
   - **DDPM Assumption**: In DDPM, the reverse process is modeled as a gradual denoising over many small steps, where each step is crucial for reducing the noise incrementally.
   - **DDIM Assumption**: DDIM assumes that it is possible to compute the state of the system at earlier timesteps directly, without the need for small incremental steps. This involves assuming that the latent variables at different timesteps can be directly related through a deterministic mapping, allowing for fewer steps to be used in the sampling process.

### Mathematical Formulation of Assumptions:

To be precise, the assumptions allow us to move from the DDPM update rule:

$$
p_\theta(x_{t-1} | x_t) = \mathcal{N}(x_{t-1}; \mu_\theta(x_t, t), \sigma_t^2 \mathbf{I})
$$

To the DDIM update rule:

![Equation](https://latex.codecogs.com/png.latex?x_%7Bt-1%7D%20%3D%20%5Csqrt%7B%5Cbar%7B%5Calpha%7D_%7Bt-1%7D%7D%20%5Ccdot%20%5Chat%7Bx%7D_0(x_t)%20%2B%20%5Csqrt%7B1%20-%20%5Cbar%7B%5Calpha%7D_%7Bt-1%7D%20-%20%5Ceta%5E2%7D%20%5Ccdot%20%5Cepsilon_%5Ctheta(x_t%2C%20t)%20%2B%20%5Ceta%20%5Ccdot%20%5Cmathbf%7Bz%7D)


Where:
- $\hat{x}_0(x_t)$ is the model's prediction of the original $x_0$ from $x_t$.
- $\eta$ is a tuning parameter controlling the level of stochasticity (with $\eta = 0$ yielding a deterministic process).

### Summary of Assumptions:
- **Reinterpretation of the reverse process as an ODE.**
- **Non-Markovian assumption allowing direct dependencies across multiple steps.**
- **Deterministic mapping between states at different timesteps.**
- **Controlled variance for optional stochasticity.**
- **Equivalence in the forward process, ensuring consistency between DDPM and DDIM.**
