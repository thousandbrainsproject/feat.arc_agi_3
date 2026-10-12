![](docs/figures/overview/logo.png)

# Monty Meets ARC-AGI-3

This is a fork of [tbp.monty](https://github.com/thousandbrainsproject/tbp.monty) created by Team Mighty Mouse (Dale, Jeremy, and Hojae) during the August 2026 Rome Focus Week. It connects Monty to [ARC-AGI-3](https://arcprize.org/arc-agi/3), an interactive reasoning benchmark of turn-based grid games. The goal was not to solve ARC-AGI-3, but to build a platform for exploring these games with Monty.

Watch the presentation: [2026/08 - Rome Focus Week: Team Mighty Mouse (ARC-AGI-3)](https://www.youtube.com/watch?v=QsTJY-83EUg&t=168s)

## What's Included

- **ARC-AGI-3 simulator** (`src/tbp/monty/simulators/arc_agi/`): wraps the official `arc_agi` toolkit in offline mode. Each game is treated as an object. `SetSensorPose` moves a sensor patch over the current 64x64 frame without advancing the game, while game actions (Up/Down/Left/Right, Use, Click, Undo, Reset) change the game state. The sensor's location is `(x, y, game_step)`, so each game step is stored along the z-axis and Monty learns a temporal model of the game. The game resets automatically on a win or game over.
- **Games** (`arc_agi/games/`): the 25 public ARC-AGI-3 games, plus three simple mazes (`sm00`, `sm01`, `sm02`) built during the hackathon with the game engine.
- **Sensor modules** (`src/tbp/monty/frameworks/models/arc_sensor_module.py`): `ArcPatchSensorModule` extracts a palette histogram and a color transition density from each patch. `ArcChangeSensorModule` (work in progress) detects pixels that changed between frames and proposes them as goals to look at.
- **Motor policies** (`src/tbp/monty/frameworks/models/motor_policies.py`): a snake scan over the frame, a random walk, and a goal-driven walk toward changed regions. Game actions are currently chosen at random. No policy tries to win a game yet.
- **Compositional game maps** (`src/tbp/monty/frameworks/models/arc_compositional.py`): a child learning module learns individual sprites, and a parent learning module learns game maps composed of those sprites. Sprite locations are currently provided by an oracle derived from the game definitions.

## Setup

The `arc-agi` package requires Python 3.13 or later, so the conda environments (`environment.yml`, `environment_arm64.yml`), which use Python 3.8, will not work for this fork. Use [`uv`](https://docs.astral.sh/uv/) instead. This overrides the note in `UV_PROTOTYPE.md`.

```bash
uv sync
```

Set the usual Monty environment variables. Trained models are saved under `$MONTY_MODELS/my_trained_models/`, the sprite dataset is written to `$MONTY_DATA/arc/sprite_children/v2/`, and evaluation logs go to `$MONTY_LOGS`.

```bash
export MONTY_MODELS=~/tbp/results/monty/pretrained_models
export MONTY_DATA=~/tbp/data
export MONTY_LOGS=~/tbp/results/monty
```

The evaluation configs log to Weights & Biases. Set `WANDB_MODE=disabled` (or `offline`) if you don't want to log to W&B.

## Running Experiments

Run experiments with `uv run python run.py experiment=<name>`. Training must run before its matching evaluation, because evaluation loads the trained model.

**Game classification** (recognize which game is being played):

```bash
uv run python run.py experiment=arc_train   # 8x8 patches, all 28 games
uv run python run.py experiment=arc_eval
```

For 4x4 patches on a subset of 17 favorite games, run `arc_train_4k_favs` followed by `arc_eval_4k_favs`.

**Compositional game maps** (learn sprites, then game maps composed of sprites):

```bash
uv run python tools/generate_arc_sprite_dataset.py   # writes the sprite dataset to $MONTY_DATA
uv run python run.py experiment=supervised_pre_training_arc_sprite_children
uv run python run.py experiment=supervised_pre_training_arc_comp_models
```

# Welcome to the Monty Repository!

*An open-source, sensorimotor learning system following the principles of the neocortex.*

[![License](https://img.shields.io/badge/License-MIT-blue.svg)](https://opensource.org/licenses/mit)
![PyPI - Python Version](https://img.shields.io/badge/python-3.8-blue)
[![](https://github.com/thousandbrainsproject/tbp.monty/actions/workflows/monty.yml/badge.svg)](https://github.com/thousandbrainsproject/tbp.monty/actions/workflows/monty.yml)

This repository contains the first implementation of a **sensorimotor learning system** from the **Thousand Brains Project**. We lovingly call it **Monty** after Vernon Mountcastle, who proposed cortical columns as a repeating functional unit across the neocortex.

This is an open-source project that was initially started at Numenta. The Thousand Brains Project is now an independent non-profit, partially funded by the Gates Foundation.

Please find our [**full documentation** here](https://docs.thousandbrains.org/)

Our [**API documentation** here](https://api-monty.thousandbrains.org).

# Getting Started

You can find detailed instructions on how to install the project requirements and how to get started [here](https://docs.thousandbrains.org/docs/getting-started)

# Current Performance
We regularly evaluate this system against a set of sensorimotor tasks, and report results in **[benchmark experiments](./benchmarks/)**. Any time a functional change is made to the code, these experiments are rerun, and results are updated. Configs for these experiments can be found in the [src/tbp/monty/conf/experiment/](./src/tbp/monty/conf/experiment/) folder.

You can find our current performance on these benchmarks as well as an explanation of them [here](https://docs.thousandbrains.org/docs/benchmark-experiments).


# Contributing

Are you interested in contributing? Check out our tips and guidelines [here](https://docs.thousandbrains.org/docs/contributing).

Before contributing, please sign our Contributor License Agreement (CLA). You can find the CLA and guidelines [here]( https://docs.thousandbrains.org/docs/contributor-license-agreement).

# Disclaimer
This is not production-ready code. It is an **early beta version** that is under active development. This early beta version is functional but evolving. Expect frequent changes as we develop core features.

You can find a list of the systems **current capabilities and application criteria** [here](https://docs.thousandbrains.org/docs/application-criteria).

You can find our **project road map** and details on the next features we are working on [here](https://docs.thousandbrains.org/docs/project-roadmap).

# More Information and Updates
As mentioned above, we have extensive **documentation** of this project [here](https://docs.thousandbrains.org/).

[![](docs/figures/overview/docs_screenshot.png)](https://docs.thousandbrains.org/)

We also publish our meeting recordings on **YouTube** on the [Thousand Brains Project channel](https://www.youtube.com/@thousandbrainsproject).

[![](docs/figures/overview/youtube_screenshot.png)](https://www.youtube.com/@thousandbrainsproject)

If you want to use this code, contribute to it, ask questions or propose ideas, please consider joining [our discourse channel](https://forum.thousandbrains.org/).

[![](docs/figures/overview/discourse_screenshot.png)](https://forum.thousandbrains.org/)

If you would like to receive updates, follow us on [Bluesky](https://bsky.app/profile/thousandbrains.org) or [Twitter](https://x.com/1000brainsproj) or [LinkedIn](https://www.linkedin.com/company/thousand-brains-project/).

If you have further questions or suggestions for collaborations, don't hesitate to contact us directly at **info@thousandbrains.org**.

# Citing the Project
If you're writing a publication that references the Thousand Brains Project, please cite 

[TBP white paper](https://arxiv.org/abs/2412.18354):
```
@misc{thousandbrainsproject2024,
      title={The Thousand Brains Project: A New Paradigm for Sensorimotor Intelligence},
      author={Viviane Clay and Niels Leadholm and Jeff Hawkins},
      year={2024},
      eprint={2412.18354},
}
```


If you would like to refer to Monty's capabilities and advantages over deep learning, please cite

[Thousand-Brains Systems: Sensorimotor Intelligence for Rapid, Robust Learning and Inference](https://doi.org/10.1162/NECO.a.1508):
```
@article{thousand-brains_systems_2026,
	title = {Thousand-{Brains} {Systems}: {Sensorimotor} {Intelligence} for {Rapid}, {Robust} {Learning} and {Inference}},
	volume = {38},
	issn = {0899-7667, 1530-888X},
	url = {https://direct.mit.edu/neco/article/38/6/845/136222/Thousand-Brains-Systems-Sensorimotor-Intelligence},
	doi = {10.1162/NECO.a.1508},
	number = {6},
	urldate = {2026-06-10},
	journal = {Neural Computation},
	author = {Leadholm, Niels and Clay, Viviane and Knudstrup, Scott and Lee, Hojae and Hawkins, Jeff},
      month = may,
	year = {2026},
	pages = {845--896},
}

```

If you would like to reference the theory behind this novel AI approach, here you can find a list of [neuroscience theory papers](https://docs.thousandbrains.org/docs/further-reading#our-papers).


# License

The MIT License. See the [LICENSE](LICENSE) for details.
