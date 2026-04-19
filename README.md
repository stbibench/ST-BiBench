<div align="center">

<!-- 1. LOGO -->

<img src="static/images/logo.png" width="150px">

<!-- 2. TITLE -->

<h1>ST-BiBench</h1>
<p align="center">
  <b>Benchmarking Multi-Stream Multimodal Coordination in Bimanual Embodied Tasks for MLLMs</b>
</p>

<!-- 3. AUTHORS (使用纯 HTML 确保渲染整洁) -->

<div>
  <a href="https://openreview.net/profile?id=%7EXin_Wu13">Xin Wu</a><sup>1*</sup>, 
  <a href="https://liang-zx.github.io/">Zhixuan Liang</a><sup>2*</sup>, 
  <a href="https://mayuelala.github.io/">Yue Ma</a><sup>3,4†</sup>, 
  <a href="https://aaron617.github.io/">Mengkang Hu</a><sup>2</sup>, 
  <a href="https://openreview.net/profile?id=~Zhiyuan_Qin1">Zhiyuan Qin</a><sup>4</sup>, 
  <a href="https://openreview.net/profile?id=~Xiu_Li1">Xiu Li</a><sup>1†</sup>
</div>

<!-- 4. INSTITUTIONS -->

<div style="margin-top: 10px;">
  <sup>1</sup>Tsinghua University    
  <sup>2</sup>The University of Hong Kong    
  <sup>3</sup>HKUST    
  <sup>4</sup>Beijing Innovation Center of Humanoid Robotics
</div>

<!-- 5. NOTES -->

<div style="margin-top: 5px; font-size: 0.9em;">
  <sup>*</sup>Equal Contribution    <sup>†</sup>Corresponding Authors
</div>

<br>

<!-- 6. BADGES (优化后的徽章代码) -->
<div>
`<a href="https://stbibench.github.io/"><img src="https://img.shields.io/badge/Project-Page-blue?style=for-the-badge&logo=googlechrome&logoColor=white">``</a>`
`<a href="https://arxiv.org/abs/2602.08392"><img src="https://img.shields.io/badge/arXiv-2602.08392-b31b1b?style=for-the-badge&logo=arxiv&logoColor=white">``</a>`
`<a href="LICENSE"><img src="https://img.shields.io/badge/License-MIT-green?style=for-the-badge">``</a>`

</div>

## 📢 News

- **[2026-02]** ST-BiBench project page and preprint are released!
- **[2026-02]** **Evaluation code and benchmark assets are now available!**

---

## 💡 About ST-BiBench

**ST-BiBench** is the **first hierarchical benchmark** specifically designed to systematically evaluate the **bimanual coordination** capabilities of Multimodal Large Language Models (MLLMs).

While current research in embodied AI has made significant strides in single-arm manipulation, bimanual coordination remains a formidable challenge. It requires more than just parallel execution; it demands **rigorous spatiotemporal synchronization** and **dynamic role assignment** to navigate complex kinematic constraints and prevent self-collisions.

### 🌟 Key Features

- **Hierarchical Evaluation Framework:** Deconstructs bimanual tasks into three distinct levels of abstraction:
  - **Tier 1 (Strategic Coordination Planning):** Functions as the core reasoning engine to evaluate high-level cross-modal alignment and the ability to decompose long-horizon instructions into parallel or sequential collaborative primitives.
  - **Tier 2 (Foundational Spatial Grounding):** Addresses the "proximity paradox" by verifying workspace constraint awareness and resolving spatial-semantic conflicts during arm allocation.
  - **Tier 3 (Fine-Grained Action Control):** Tests the frontiers of dense modality fusion by requiring the direct synthesis of 16-dimensional continuous action modalities for precise end-to-end actuation.
- **Vision-Driven Agent Pipeline:** A structured closed-loop reasoning framework where the MLLM functions as a central "brain" for iterative perception, reasoning, and action.
- **Extensive Empirical Study:** Analysis of over **30+ state-of-the-art models**, revealing a significant **"reasoning-actuation gap"** in current foundation models.

<p align="center">
  <img src="static/images/framework_overview.png" width="95%">
  <br>
  <em>Figure 1: The hierarchical evaluation framework of ST-BiBench.</em>
</p>

<p align="center">
  <img src="static/images/agent_pipeline.png" width="95%">
  <br>
  <em>Figure 2: The vision-driven agent pipeline for multimodal perception and reasoning.</em>
</p>

---

## 🛠️ Installation and Deployment

<!-- *Code release is in progress. Stay tuned!* -->

Installation tutorial can be found at [RoboTwin installation guide](https://github.com/RoboTwin-Platform/RoboTwin). Since this project is built upon the RoboTwin framework, the installation process is largely identical.

#### 1. Install Vulkan (if not installed)

This project requires Vulkan for rendering. Please ensure the drivers and tools are installed:

```bash
sudo apt update
sudo apt install libvulkan1 mesa-vulkan-drivers vulkan-tools
```

**Verification:**
Check the installation by running:

```bash
vulkaninfo
```

### 2. Basic Environment Setup

First, prepare a Conda environment and clone the repository:

```bash
# Create and activate environment
conda create -n stbibench python=3.10 -y
conda activate stbibench

# Clone the repository
git clone https://github.com/stbibench/ST-BiBench.git
cd ST-BiBench
```

Next, run the installation script to install basic dependencies and **CuRobo**:

```bash
bash script/_install.sh
```

#### Troubleshooting & Notes:

* **CuRobo Config Path:** If you encounter a `curobo` configuration path issue, try running:
  ```bash
  python script/update_embodiment_config_path.py
  ```
* **Manual Installation:** If you encounter any problems during the automated script, please refer to the manual installation section in the original RoboTwin docs.
* **Pytorch3D:** If the installation of `pytorch3d` fails and you are not using 3D data, it will not affect the core functionality of the project.
* **FFmpeg:** This project requires FFmpeg. Please ensure it is installed by checking:
  ```bash
  ffmpeg -version
  ```

  If it is not installed, please visit [https://ffmpeg.org/](https://ffmpeg.org/) for instructions.

### 3. Download Assets

Download the required assets (RoboTwin-OD, Texture Library, and Embodiments) by running the following command.

*Note: If you encounter any rate-limit issues with Hugging Face, please log in to your account first:*

```bash
# Optional: Login to Hugging Face
huggingface-cli login

# Download assets
bash script/_download_assets.sh
```

#### Assets Folder Structure

After downloading, your `assets` folder should follow this structure:

```text
assets
├── background_texture
├── embodiments
│   ├── embodiment_1
│   │   ├── config.yml
│   │   └── ...
│   └── ...
├── objects
└── ...
```

## 🚀 Quick Start

This section provides a quick start guide for configuring your models and running the evaluation benchmarks.

### 1. Model Configuration

Before running the evaluation scripts, you must configure your model settings in `script/remote_model.py`. This script handles both locally deployed open-source models and remote API-based models.

#### Local Deployment

By default, the project assumes local models are served via an OpenAI-compatible server (e.g., vLLM or LMDeploy) at:
`http://localhost:8000/v1`

#### API Configuration

If you are using proprietary models (e.g., GPT, Claude, Gemini) or specific remote providers, you need to edit `script/remote_model.py` to provide your **API Key** and **Base URL**.

Locate the following block and update the placeholders:

```python
# script/remote_model.py

if "gpt" in self.model_name:
    self.model = OpenAI(
        api_key="YOUR_API_KEY",
        base_url="YOUR_API_URL"
    )
elif "claude" in self.model_name:
    # Set your Claude API details here
    ...
```

---

### 2. Running Evaluation

We provide evaluation scripts for three different hierarchical levels. Ensure your environment is activated and you are in the project root directory.

#### Level 1: Dual-Arm Spatial Reasoning

This level evaluates the model's ability to understand spatial relationships in different scene complexities.

**Command:**

```bash
python script/run_eval_spatial.py --setting <SETTING> --gpu <GPU_ID> --model <MODEL_NAME>
```

* `--setting`: Choose from `sparse`, `cluttered`, or `dense`.
* `--gpu`: GPU ID(s) to use (e.g., `0`).
* `--model`: The name/path of the model configured in `remote_model.py`.

---

#### Level 2: High-Level Action Planning

This level evaluates the model's ability to generate long-horizon task plans.

**Command:**

```bash
python script/run_eval_high_level.py --task <TASK_NAME> --gpu <GPU_ID> --model <MODEL_NAME>
```

**Supported Tasks (`--task`):**

| Robotic arm types      | Task Names                                                                                                                                                |
| :--------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **ARX-X5**       | `handover_mic`, `blocks_ranking_size`, `hanging_mug`, `place_cans_plasticbox`, `place_burger_fries`, `stack_blocks_three`, `handover_block` |
| **Franka-Panda** | `blocks_ranking_rgb`, `place_object_basket`, `place_bread_skillet`, `stack_bowls_three`, `blocks_tower`, `blocks_cross_shape`                 |
| **Piper**        | `put_bottles_dustbin`                                                                                                                                   |

---

#### Level 3: Low-Level End-Effector Control

This level evaluates fine-grained control and precise end-effector positioning.

**Command:**

```bash
python script/run_eval_low_level.py --task <TASK_NAME> --gpu <GPU_ID> --model <MODEL_NAME>
```

**Supported Tasks (`--task`):**

* `place_object_scale`
* `place_burger_fries`
* `grab_roller`
* `stack_blocks_two`
* `place_bread_skillet`

---

**Config Path Issue:** If you encounter issue like `Error setting up environment for round0, episode 0: 'Robot' object has no attribute 'left_planner'`, try running:

```bash
python script/update_embodiment_config_path.py
```

#### Example Usage

To evaluate a model named `gpt-4o` on the `cluttered` spatial reasoning setting using GPU 0:

```bash
python script/run_eval_spatial.py --setting cluttered --gpu 0 --model gpt-4o
```

## 🖋️ Citation

**If you find ST-BiBench useful in your research, please cite our work:**

```
@misc{wu2026stbibenchbenchmarkingmultistreammultimodal,
      title={ST-BiBench: Benchmarking Multi-Stream Multimodal Coordination in Bimanual Embodied Tasks for MLLMs}, 
      author={Xin Wu and Zhixuan Liang and Yue Ma and Mengkang Hu and Zhiyuan Qin and Xiu Li},
      year={2026},
      eprint={2602.08392},
      archivePrefix={arXiv},
      primaryClass={cs.RO},
      url={https://arxiv.org/abs/2602.08392}, 
}
```

## 🤝 Acknowledgements

ST-BiBench is built upon the great efforts of the open-source community. We would like to express our gratitude to the following projects:

- **[RoboTwin 2.0](https://github.com/robotwin-Platform/robotwin)**: We utilize RoboTwin 2.0 as our primary simulation platform. While the original framework is designed for evaluating VLA (Vision-Language-Action) policies, we adapted its high-quality bimanual environments, assets, and task configurations to support the direct evaluation of Multimodal Large Language Models (MLLMs). We also customized several task environments to better align with our hierarchical coordination tiers.
- **[EmbodiedBench](https://github.com/EmbodiedBench/EmbodiedBench)**: Our agent's decision-making pipeline is inspired by EmbodiedBench. We adapted its structured evaluation paradigm—originally designed for various single-arm tasks—and re-engineered the top-level logic, including environment-agent interaction and API communication, to facilitate bimanual manipulation within the RoboTwin.

We sincerely thank the authors of these projects for their pioneering contributions to the field.
