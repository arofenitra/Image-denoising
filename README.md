# Image-denoising
This covers Image denoising techniques such as Total Variation, isotropic diffusion and anisotropic diffusion
## Total Variation
This model is introduced by Rudin, Osher and Fatemi in 1992 [[Rudin, L., Osher, S. and Fatemi, E. (1992) Nonlinear Total Variation Based Noise Removal Algorithms. Physica D, 60, 259-268.](https://doi.org/10.1016/0167-2789(92)90242-F)]. Its key insight was to model a noisy image as a function of bounded variation, not as a collection of pixels.  
### Core idea: Preserving Edges  
The goal of Total Variation (TV) denoising is to :
- Remove noise and small scale fluctuations.
- Preserve sharp edges and boundaries. 
by exploiting TV, which is a measure of how much a function oscillates, of a noisy image is artificially high.  TV model aims to find a denoised image which has smaller TV while remaining very close to the original noisy image.  
### Mathematical Foundation: Variational Calculus
The TV denoising problem is formulated as an optimization problem: 
$$\hat{u}=\arg \min _{u\in BV(\Omega)}\int_{\Omega}\left(\|\nabla u\| + \frac{\lambda}{2}(u-f)^2\right)dx$$
where 
- $BV(\Omega)$ is the set of functions with bounded variation over the domain $\Omega$.
- $\hat{u}:  \Omega \subset \mathbb{R}^2 \to \mathbb{R}$ is the denoised image or unknown clean image we want to recover. 
- $f: \Omega \subset \mathbb{R}^2 \to \mathbb{R}$ is the noisy image.
- $E: (\Omega\to \mathbb{R})\to \mathbb{R}$, $E(u)=\int_\Omega\left(\|\nabla u\|-\frac{\lambda}{2}(u-f)\right)dx$ is an Energy Functional.
- $\int_{\Omega}\|\nabla u\|dx$ is the total variation of $u$.
- $L(x,u,\nabla u) = \|\nabla u\|-\frac{\lambda}{2}(u-f)$  

When $\lambda \to 0$, the output will remove fine details and cause staircasing effect. However, when $\lambda \to \infty$, the output will be very close to the original image and more noise remains.
### Solution of the minimization problem
From [Calculus of Variation](https://en.wikipedia.org/wiki/Calculus_of_variations), the Euler-LAgrange Equation for ROF model is:
$$\frac{\partial L}{\partial u} - \nabla \cdot \frac{\partial L}{\partial (\nabla u)}=0$$
$$\lambda (u-f)-\nabla\cdot \left(\frac{1}{\|\nabla u\|}\nabla u \right)=0$$ 
where
- $c=\frac{1}{\|\nabla u\|}$ is the diffusivity term, which is small at an edge(where $\|\nabla u\|$ is large) i.e. diffusion is stopped across edges. 
- $\lambda (u-f)$: This terms pulls the solution $u$ back towards original data $f$.
### Discretization

The Euler-Lagrange equation is discretized using finite differences. The image is represented as a grid of pixels, and the derivatives are approximated using FDM. The resulting system of equations is solved using the Gauss-Seidel method, which is an iterative method for solving linear systems. 
- Forward differences to compute gradient:
$$(\partial_x u)_{i+\frac{1}{2}, j} \approx u_{i+1, j} - u_{i, j}$$
$$(\partial_y u)_{i, j+\frac{1}{2}} \approx u_{i, j+1} - u_{i, j}$$
- Computing Magnitude:
$$\|(\nabla u)_{i+\frac{1}{2}, j}\|\epsilon = \sqrt{(u_{i+1,j} - u_{i,j})^2 + (\text{average }y)^2 + \epsilon}$$

### Advantages and Limitations
#### Advantages
Advantages include edge preservation (does not blur edges like linear filters), well posed model (has solution for weak formulation) and Foundation for Advanced Models (Inspired variational methods in image processing).
#### Limitations
Limitations includes staircasing effect (piecewise constant regions, even in areas that should be smoothly varying), loss of texture (can oversmooth and remove fine-scale textures), and computational complexity (requires solving a PDE, which can be computationally expensive).