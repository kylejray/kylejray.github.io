---
layout: standalone

permalink: /fredkin/
exclude: true

sa_title : Momentum Computing
sa_home : /momentum/
---
## “Non-Markovian Momentum Computing: Thermodynamically Efficient and Computation Universal”
Practical, useful computations are instantiated via physical processes. Information must be stored and updated within a system's configurations, whose energetics determine a computation's cost. To describe thermodynamic and biological information processing, a growing body of results embraces rate equations as the underlying mechanics of computation. Strictly applying these continuous-time stochastic Markov dynamics, however, precludes a universe of natural computing. Within this framework, operations as simple as a NOT gate, flipping a bit, and swapping bits are inaccessible. We show that expanding the toolset to continuous-time hidden Markov dynamics substantially removes the constraints, by allowing information to be stored in a system's latent states. We demonstrate this by simulating computations that are impossible to implement without hidden states. We design and analyze a thermodynamically-costless bit flip, providing a counterexample to rate-equation modeling. We generalize this to a costless Fredkin gate—a key operation in reversible computing that is Turing complete (computational universal). Going beyond rate-equation dynamics is not only possible, but necessary if stochastic thermodynamics is to become part of the paradigm for physical information processing. The increased analytical challenges are readily addressed with recently-introduced spectral decomposition methods for nondiagnonalizable dynamics.


K. J. Ray, G. W. Wimsatt, A. B. Boyd, and J. P. Crutchfield,
- [Non-Markovian Momentum Computing: Thermodynamically Efficient and Computation Universal](https://journals.aps.org/prresearch/abstract/10.1103/PhysRevResearch.3.023164)

Selected animations of continuous-time thermodynamically-free Fredkin gate:

<iframe width="540" height="540" align='right' src="https://drive.google.com/file/d/1KHs3RkkAYjDXOaH9dKPVRGq3TtJvNloJ/preview">
</iframe>

- Particle ensemble undergoing the Fredkin-gate protocol, with initial conditions drawn from the equilibrium distribution of the storage potential. We see that the momentum the particles pick up while traveling to the point acts as a memory, allowing us to continue to control the disparate initial conditions independently. Here, we easily see how the action of two parabolic wells with differing characteristic frequencies allows for 110 and 101 to swap places, while 100 and 111 complete a full cycle, returning to their initial coordinates.


<iframe width="540" height="540" align='right' src="https://drive.google.com/file/d/1FzA94lE3TPSRL8c5sgKRoEX8bAVxDmH_/preview">
</iframe>

- (Left) Particle ensemble undergoing the Fredkin-gate protocol, with initial conditions drawn from the equilibrium distribution of the storage potential. Looking at the dynamics projected onto the y-z plane allows for a clear view of how the controlled-swap operation takes place in the computational subspace. The grey points represent storage bits that are not exposed to the computational potential. Note especially that the particles not only end in the appropriate informational quadrant, but also their initial distribution over that quadrant—which is a necessary condition for the net thermodynamic work to vanish. (Right) An illustrative animation of how a uniform distribution over y and z changes over the protocol duration. Each colored square represents a uniform distribution in the corresponding y,z quadrant, with z = 2 and vx = vy = vz = 0.

<iframe width="540" height="540" align='right' src="https://drive.google.com/file/d/1VzF-DruEbuUmd9gNMANmkHsYlGUKJnQC/preview">
</iframe>

 - Particle ensemble undergoing the Fredkin-gate protocol, with initial conditions drawn from the equilibrium distribution of the storage potential. The y' and z' phase spaces allows us to see the most relevant projection of the rotation that takes place in the full phase space. When the computational protocol turns on, the particles associated with x > 0 stop rotating about local storage well minima, and start rotation about the minima of Vcomp. Side by side, we clearly see a full period in the z' subspace and only half a period in the y' subspace—as required to perform the Fredkin gate.

