# doom-flow-rl

Teach an agent to play a **Doom-like** combat loop in [Flow](https://github.com/flooooooooooow/flow),
using Flow's own RL APIs (`stdlib/ai.flow`), the same way the upstream
[game-ai tutorial](https://github.com/flooooooooooow/flow/blob/main/docs/tutorials/game-ai.md)
trains Snake / Flappy / Pong.

Engine port (full Doom in Flow, public GIF CI):
[doom-flow](https://github.com/godofecht/doom-flow).

This repo does **not** wrap the full engine as a Gym env. Flow's pattern is a
headless re-sim of the rules you care about, then `q_select` / `q_update`
(or `mlp_reinforce`) in pure Flow. Here that means a compact arena: turn,
strafe-forward, hitscan fire, one chasing imp.

## Run

```bash
# Flow checkout next to this repo (or ~/flow)
../flow/flow run q_doom.flow
# or:
~/flow/flow run q_doom.flow
```

Exits `0` only if the greedy policy's kill-rate beats a random baseline by a
wide margin (same regression style as `examples/ai/q_snake.flow`).

## What's Flow-native

```flow
q_init(1337 as u32)
let a: i32 = q_select(s, 4, eps)                 # turn L/R, forward, fire
q_update(s, a, r, s_next, 4, alpha, gamma)
q_update_terminal(s, a, r, alpha)                # death / frag
```

State is a ~9-bit encoding (wall ahead, line-of-sight, enemy side, distance
and health buckets) so the Q-table in `ai.flow` stays dense.

## Next steps toward full doom-flow

- Expose observation hooks from doom-flow (player health, enemy bearings)
  and drive actions through `DOOMFLOW_KEYSCRIPT` / a tick API.
- Swap tabular Q for `mlp_reinforce` once the observation is larger.
- Keep training headless; use doom-flow's `scripts/record_gif.sh` only for
  evaluation footage.
