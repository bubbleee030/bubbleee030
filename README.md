<p align="center">
  <img src="assets/banner.svg" alt="Bubble: safer language models and robots that follow spoken instructions" width="100%">
</p>

National University of Kaohsiung · cmwang16@gmail.com · [繁體中文](README.zh-TW.md)

## LLM safety and alignment

Safe reinforcement learning for an 8B Traditional Chinese customer-service model. A LoRA policy is trained
with PPO-Lagrange, a constrained RL method: it maximizes a learned reward model (helpfulness) while
keeping a learned cost model (safety) under a threshold, and a Lagrange multiplier sets how hard the
constraint pushes. Prompts come from a policy-conditioned red-team generator, and a blinded evaluation
judges the result.

What the RL work found:

- The first PPO-Lagrange run was indistinguishable from the untrained model. Four defects, each measurable
  before training, kept the constraint from acting: a leaky cost-model split (true recall of unsafe
  responses was 20.1%), a cost threshold of 0.0 that every batch already met (the multiplier decayed to
  0.0114), a reward model that scored unsafe compliance 2.121 above a safe refusal, and an unstable actor
  learning rate.
- The multiplier follows the threshold. It decays when the threshold is always met, saturates at its cap
  when it is never met, and adapts only when the threshold is reachable and reached.

```mermaid
flowchart LR
    A["Policy-conditioned<br/>harmful-prompt generation"] --> B["Human + LLM<br/>preference annotation"]
    B --> C["Reward model +<br/>cost model"]
    C --> D["PPO-Lagrange<br/>(LoRA adapter)"]
    D --> E["Blinded five-arm<br/>evaluation"]
```

| Project | What it is | One finding |
|---|---|---|
| [RLHF_Customer](https://github.com/bubbleee030/RLHF_Customer) | Reward/cost models and PPO-Lagrange for safety, with a preregistered held-out set | Adapter on top of a system prompt: **+24.5 pp** safe outcomes, 95% CI [13.7, 36.3] (163 prompts). Replacing the prompt with the adapter: inconclusive. |
| [airflow-datagen-harmful-prompt](https://github.com/bubbleee030/airflow-datagen-harmful-prompt) | Turns a written safety policy into severity-graded red-team prompts with provenance | Against 50 blind human labels, an untuned `gpt-oss-20b` judge caught **0.91** of violations. The safety-tuned 120B model caught 0.68–0.70. |
| [sft_project](https://github.com/bubbleee030/sft_project) | Data pipeline for a Traditional Chinese LLM-as-a-Judge: SimHash dedup, Traditional/Simplified filtering, ablations | In one run only **43.8%** of raw judge records were usable. 32.5% were near-duplicates, 23.7% malformed. |
| [argilla_project](https://github.com/bubbleee030/argilla_project) | Pairwise-preference annotation platform with automatic backup | Annotation exports are converted into DPO pairs and judge test sets in `sft_project`. |

<table>
  <tr>
    <td width="50%" align="center" valign="top">
      <img src="https://raw.githubusercontent.com/bubbleee030/RLHF_Customer/main/docs/figures/four_estimands.svg" alt="Paired safe-outcome effects with 95% intervals for each estimand" width="100%">
      <br><sub>RLHF_Customer. Paired effects with 95% intervals. Intervals that cross zero are marked inconclusive.</sub>
    </td>
    <td width="50%" align="center" valign="top">
      <img src="https://raw.githubusercontent.com/bubbleee030/sft_project/main/docs/figures/1_overall_status_distribution.png" alt="Share of raw judge records that were valid, duplicate or malformed" width="100%">
      <br><sub>sft_project. Fewer than half of the raw records were usable.</sub>
    </td>
  </tr>
</table>

<p align="center">
  <img src="https://raw.githubusercontent.com/bubbleee030/RLHF_Customer/main/docs/figures/lambda_regimes.svg" alt="Terminal Lagrange multiplier per run against its cap" width="60%">
  <br><sub>RLHF_Customer: terminal Lagrange multiplier for each run, against its cap.</sub>
</p>

## Robot learning

| Project | What it is |
|---|---|
| [voice-pick-vla](https://github.com/bubbleee030/voice-pick-vla) | A voice-commanded pick-and-place system on two machines. An RDT VLA policy moves the arm, and a tactile LSTM on a Jetson AGX controls the gripper. It has a fallback ladder (VLA, then YOLO pipeline, then taught pose) and a web dashboard. |
| [VLA](https://github.com/bubbleee030/VLA) | The earlier work: RDT fine-tuning, dataset conversion, tactile-image augmentation and tactile-vs-baseline experiments. |

<p align="center">
  <img src="https://raw.githubusercontent.com/bubbleee030/voice-pick-vla/main/docs/images/ui_dashboard.png" alt="voice-pick-vla operator dashboard: cameras, claw YOLO view, tactile sensor trace, arm monitor and voice input" width="100%">
  <br><sub>voice-pick-vla operator dashboard. Two RealSense views, claw camera with YOLO, live tactile trace, arm state and voice input.</sub>
</p>

## Also

- [Peek](https://github.com/bubbleee030/Peek): a macOS menu-bar app that lists what is inside folders and archives when you press space in Finder (Swift, with a downloadable release).
- [Caffeine](https://github.com/bubbleee030/Caffeine): a fork that adds lid-closed mode, launch at login and Traditional Chinese localization.

## Tools I work with

Python · PyTorch · Hugging Face · LLaMA-Factory · Airflow · Docker · Slurm / Singularity ·
Swift / SwiftUI · TypeScript · Flask / Socket.IO · RealSense · YOLO · Jetson
