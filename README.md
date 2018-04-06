# gym-lattice

[![Build Status](https://travis-ci.org/ljvmiranda921/gym-lattice.svg?branch=master)](https://travis-ci.org/ljvmiranda921/gym-lattice)
![python 3.4+](https://img.shields.io/badge/python-3.4+-blue.svg)

An HP 2D Lattice Environment with a Gym-like API for the protein folding
problem

This is a Python library that formulates Lau and Dill's (1989)
hydrophobic-polar two-dimensional lattice model as a reinforcement learning
problem. It follows OpenAI Gym's API, easing integration for reinforcement
learning solutions. This library only implements a two-dimensional square
lattice, but different lattice structures will be done in the future.

## Dependencies

- numpy==1.14.2
- gym==0.10.4
- pytest==3.2.1 *(dev)*

## Installation

First, clone this repository with the following command:

```
git clone https://github.com/ljvmiranda921/gym-lattice.git
```

and then install via `pip`:

```
pip install -e .
```

## Usage

In this environment, there are four possible actions that your agent can
perform: `0` (left), `1` (down), `2` (up), and `3` (right). The number
choices may seem funky at first but just remember that it maps to the
standard vim keybindings.

Let's try this out with a random agent on the protein sequence `HHPHH`!

```python
from gym_lattice.envs import Lattice2DEnv
from gym import spaces
import numpy as np

np.random.seed(42)

seq = 'HHPHH' # Our input sequence
action_space = spaces.Discrete(4) # Discrete space from 0 to 3
env = Lattice2DEnv(seq)

for i_episodes in range(5):
    env.reset()
    while True:
        # Sample randomly from action space
        action = action_space.sample()
        obs, reward, done, info = env.step(action)
        if done:
            print("Episode finished! Reward: {} | Collisions: {} | Actions: {}".format(reward, info['collisions'], info['actions']))
            break
```

Sample output:

```
Episode finished! Reward: -2 | Collisions: 1 | Actions: ['U', 'L', 'U', 'U']
Episode finished! Reward: -2 | Collisions: 0 | Actions: ['D', 'L', 'L', 'U']
Episode finished! Reward: -2 | Collisions: 0 | Actions: ['R', 'U', 'U', 'U']
Episode finished! Reward: -3 | Collisions: 1 | Actions: ['U', 'L', 'D', 'D']
Episode finished! Reward: -2 | Collisions: 2 | Actions: ['D', 'R', 'R', 'D']
```

Observations are represented as an n-dimensional array with `1` representing
hydrophobic molecules (H), `1` for polar molecules (P), and `0` for free
spaces. If you wish to obtain the chain itself, you can do so by accessing
`info['state_chain']`

An episode ends when all polymers are added to the lattice OR if the sequence
of actions "traps" the polymer chain. Whenever a collision is detected, the
agent should enter another action. We account for the number of collisions so
you can use them when you customize your own reward functions for learning.

In addition, this environment gives **sparse rewards**, that is, reward is
only computed at the end of each episode.

## Task List
- Add test cases and set-up continuous integration
- Rendering fuctionality
- Lattice3D (?) *maybe some time in the future*
