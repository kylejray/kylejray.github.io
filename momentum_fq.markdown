---
layout: standalone

permalink: /gslmc/
exclude: true

navdata: site.data.mc_nav.yml
sa_title : Momentum Computing
sa_home : /momentum/
---
## Gigagertz Sub-Landauer Momentum Computing
This is a supplemental animation to help visualize the protocol described in the manuscript below:

K. J. Ray and J. P. Crutchfield,
- Gigahertz Sub-Landauer Momentum Computing, (2022).
- [arxiv.org:2202.07122 [cond-mat.stat-mech]](https://arxiv.org/abs/2202.07122)

<iframe width="540" height="540" align='right' src="https://drive.google.com/file/d/1JKr4OOiggspkMIBn4IaeBqyQaJ31ol2b/preview">
</iframe>



Bit swap animations of 1000 trajectories sampled from the equilibrium distribution of the storage potential from four different projections. Color encodes the initial informational state of each trajectory, with red meaning '1' and orange meaning '0'.

- Top: (left) The position space projection shows how the degrees of freedom react to the potential energy surface as it transitions between the computational and storage potential parameters, depicted by the underlying contours. The energy imparted by the change in potential is manifest in nonequilibrium oscillations of the conjugate momenta (right). This momentum space projection most clearly shows the departure and eventual return to equilibrium. Note that the transition to the storage potential imparts large oscillations in the ɸdc dimension, because the double-well (Vstore) and single-well (Vcomp) profiles are centered about different ɸdc values. However, the bulk of these oscillations are re-absorbed when the storage potential is reinstated.
- Bottom: Marginal phase space projections in both the (left) computational ɸ and (right) computationally passive ɸdc dimensions. The left plot shows how the ɸ momenta serves as a transient memory by allowing the informational states to avoid crossing in phase space. On the right, we see that the ɸdc dimension is informationally mute—trajectories corresponding to both the '0' and '1' informational states lying on top of eachother in the marginal phase space. The oscillations in this informationally mute dimension are not necessary for the computation, but are an inevitable result of the control protocol when switches between the two potential energy landscapes.
- Both: The device and protocol simulated for these animations do not represent a particularly efficient device. Instead, for illustrations purposes, a device was selected so that both the swap and the return to equilibrium could be easily seen at relatively similar timescales. For efficient devices, the particles end closer to an equilibrium distribution and the equilibration takes a couple orders of magnitude longer. Even when simulating this higher dissipation device, the time is sped up once the swap is accomplished to avoid an overly long video—an indicator that the dynamics of the swap really must happen much faster than thermal equilibration.

- [Video Download Link](https://drive.google.com/file/d/1JKr4OOiggspkMIBn4IaeBqyQaJ31ol2b/view?usp=sharing)
