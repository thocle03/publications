# Cover Letter for Submission to IEEE Transactions on Intelligent Transportation Systems

**Date:** October 9, 2026  

**To:**  
The Editor-in-Chief  
*IEEE Transactions on Intelligent Transportation Systems* (IEEE T-ITS)  

---

**Subject:** Submission of Original Research Article titled **"Topological Graph-Spectral Machine Learning for Real-Time Surrogate Estimation of Urban Traffic $\text{CO}_2$ Emissions"**

Dear Editor-in-Chief and Editorial Board Members,

We are pleased to submit our original research manuscript entitled **"Topological Graph-Spectral Machine Learning for Real-Time Surrogate Estimation of Urban Traffic $\text{CO}_2$ Emissions"** for consideration for publication as a Regular Paper in the *IEEE Transactions on Intelligent Transportation Systems*.

### 1. Research Motivation & Background
Urban road transportation accounts for over $24\%$ of global energy-related $\text{CO}_2$ emissions. While microscopic multi-agent traffic simulators (e.g., SUMO coupled with HBEFA emission models) provide high-fidelity vehicle trajectory and environmental emissions data, their super-linear computational scaling ($O(N_{\text{veh}}^{1.4} \cdot |E|)$) makes city-scale evaluations prohibitively slow (up to $6.2$ hours for dense megacity networks such as Tokyo). This high computational latency severely restricts their integration into real-time municipal digital twins, Model Predictive Control (MPC), and dynamic traffic demand management.

### 2. Key Contributions & Novelty
To overcome this computational barrier without sacrificing macroscopic physical fidelity, this paper proposes a physics-grounded graph-spectral surrogate framework. The key scientific contributions of this manuscript include:

1. **Mathematical Formulation of Dissipative Non-Normal Operators:** We model directed urban street networks as asymmetric transition operators ($\tilde{A}_0, \tilde{A}_w$) with an explicit contractive boundary dissipation factor ($\beta = 0.95$, $\rho(\tilde{A}) \le 0.95 < 1$). This rigorously establishes the convergence of discrete Hardy $H_2$ resolvent norms and unique solutions to discrete Lyapunov equations, while capturing convective directional shear via commutator norms $\Delta(\tilde{A})$ and transient queue amplification via the Kreiss matrix theorem ($K(\tilde{A})$).
2. **Analytical Sensitivity via Kato Perturbation Theory:** We derive first- and second-order analytical Kato spectral perturbation series ($\lambda^{(1)}, \lambda^{(2)}$), enabling sub-millisecond evaluation ($0.41$~ms per edge vs. $89.4$~ms for full eigensolvers, $218\times$ speedup) of localized infrastructural interventions (e.g., arterial closures, bus lane reservations).
3. **Comprehensive Empirical Validation Across 65 Global Metropolises:** Evaluated on 349 microscopic SUMO simulations, our vehicle-normalized XGBoost surrogate ($y = \text{CO}_2 / N_{\text{veh}}$) achieves an internal test $R^2 = 89.39\%$ on normalized emissions, $R^2 = 98.95\%$ on rebuilt total city emissions, and a robust 5-fold cross-validation $R^2 = 97.99 \pm 0.90\%$, outperforming spatial Graph Convolutional Networks (GCN) by $+17.75$ percentage points.
4. **Zero-Shot Transferability & Convex Barycentric Diagnostics:** On nine completely unobserved global validation cities (including Bogota, Seoul, Singapore, Montreal), the model achieves $R^2 = 99.67\%$ on representative base cases ($R^2 = 88.66\%$ across multi-load stress scenarios). We introduce a 21-dimensional convex quadratic programming barycentric projection ($d_{\mathcal{H}}$) that quantitatively certifies whether an unseen city network lies within the interpolation hull of the training corpus.
5. **Operational Real-Time Speedup:** Inference latency is reduced to under $6$~ms (an operational speedup $> 1.0 \times 10^6 \times$ over SUMO), enabling sub-second carbon emission monitoring for municipal digital twins.

### 3. Declarations and Compliance
- **Originality:** This manuscript represents original work, has not been published previously, and is not currently under consideration for publication elsewhere.
- **Authorship Approval:** All co-authors (Thomas Clerc, Prof. Pierre Uzarralde, and Prof. Alain Faye) have read and approved the final manuscript and agree with its submission to *IEEE Transactions on Intelligent Transportation Systems*.
- **Conflict of Interest:** The authors declare no competing financial or personal interests that could influence the work reported in this paper.

### 4. Suggested Topic Areas & Subject Classifications
- Graph-spectral methods and machine learning for intelligent transportation systems
- Microscopic vs. macroscopic traffic emission modeling and digital twins
- Non-normal graph dynamical systems and network perturbation theory
- Real-time environmental decision-support systems for urban decarbonization

We believe this paper will be of strong interest to the readership of *IEEE Transactions on Intelligent Transportation Systems*. Thank you for your time and consideration of our manuscript.

Sincerely,

**Thomas Clerc** (Corresponding Author)  
École Hexagone, France  
E-mail: `thomas.clerc.pro@gmail.com`  

**Prof. Pierre Uzarralde**  
Director of Artificial Intelligence Curriculum, École Hexagone, France  

**Prof. Alain Faye**  
Professor at ENSIIE & Laboratoire CEDRIC (CNAM), France  
Academic Supervisor, École Hexagone, France  
