# Classical vs. Quantum Random Walks

How are classical and quantum random walks different, how are they similar, and does one show an advantage when searching for a target on a 1D ring of nodes?

This project simulates both walks in Python and then turns them into search algorithms on a circle. The classical walk spreads diffusively. The quantum walk uses superposition and interference to spread ballistically, and that difference shows up in how quickly each version finds a random target.

## Background

### Classical random walk

Start at rest and move according to a fair coin flip (\(p = frac{1}{2}\)):

- Heads: step left (\(-1\))
- Tails: step right (\(+1\))

After \(N\) steps, repeated over many samples, the final positions form a normal distribution centered near the starting point. The spread grows diffusively:

\[
\sigma \propto \sqrt{N}
\]

This model shows up in diffusion and Brownian motion, randomized algorithms and PageRank, Markov chains, and models of stock prices and option pricing.

### Quantum random walk

The coin is placed in a superposition of heads and tails, so each step carries amplitude to the left and to the right. As the walk proceeds, those amplitudes interfere constructively and destructively. At the end, a measurement collapses the state to a single position drawn from the resulting probability distribution. The spread is ballistic:

\[
\sigma \propto N
\]

Quantum walks are a building block for algorithms in quantum computing and quantum information, including search, cryptography, and machine learning.

## What this project does

1. **Distribution check.** Simulate both walks with the same settings and compare the resulting histograms to the expected shapes and standard deviations.
2. **Search comparison.** Place the walker on a ring, pick a random target node, and count how many steps each walk needs to land on that target.

The quantum walk is implemented directly with NumPy matrix operations rather than a gate-based library such as Qiskit or Cirq.

## Method

Both distribution simulations use **500 samples** and **50 steps**.

**Classical.** At each step the walker moves \(-1\) or \(+1\) at random. Final positions across all samples are collected and histogrammed.

**Quantum.** Each step follows this sequence:

1. Apply a Hadamard gate to the coin state to put it in superposition.
2. Take the tensor product of the coin state and the position state.
3. Apply the shift operator so the position updates according to the coin.
4. Square the amplitudes and add the two coin outcomes to get a probability distribution over positions.
5. Sample a position from that distribution to collapse the state.

Because the evolution is unitary, the probabilities are already normalized.

**Search.** A target node is chosen at random on a ring. Instead of a fixed number of steps, a loop keeps walking until the measured position equals the target, and the step count is the runtime. The ring was chosen for simplicity; the same idea could be extended to other graphs or to higher dimensions.

Search runs were recorded for rings of **10, 20, 30, and 40** nodes, and mean steps were also plotted for rings of **1 to 30** nodes. Here \(N\) is the number of nodes, which is separate from the number of steps used in the standard-deviation comparison above.

## Results

The simulated distributions match the expected pictures.

| Walk | Histogram | Spread (50 steps) |
| --- | --- | --- |
| Classical | Normal distribution, peaked at the start | \(\sigma \approx 7\), consistent with \(\sqrt{50}\) |
| Quantum | Two peaks away from the center | \(\sigma \approx 27\), linear in the number of steps (constant near \(1.8\)) |

On the search task, the quantum walk is faster overall. Its mean number of steps stays on the order of the number of nodes. The classical mean grows faster as the ring gets larger, which is consistent with a diffusive search.

Two caveats come from the randomness of the experiment:

- A single classical trial can still finish before its quantum counterpart.
- If the random target is the starting node, the recorded runtime is 0 steps.

## Setup

The notebooks use Python with NumPy and Matplotlib, and they are organized so each section can be run on its own.

```bash
pip install numpy matplotlib jupyter
jupyter notebook
