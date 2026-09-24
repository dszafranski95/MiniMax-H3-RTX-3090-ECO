MiniMax H3 RTX 3090 ECO

Power-efficient MiniMax H3 runtime for a single RTX 3090 24 GB — tuned for lower power draw, lower heat and stable high-quality generation without intentionally reducing render quality.

One GPU. Same quality target. Less wasted power.

This repository is a persistent operating profile for running MiniMax H3 on 1× RTX 3090 24 GB.

It is designed to be read by both:

humans,

AI supervisor agents / coding agents / automation agents.

The supervisor reads this repository, loads the master profile, validates the machine, applies the correct MiniMax H3 settings and keeps those rules consistent across future sessions.

Repository name

Recommended GitHub repository name:

minimax-h3-rtx3090-eco

Alternative:

minimax-h3-single-3090

Recommended GitHub title / heading:

MiniMax H3 RTX 3090 ECO

Recommended GitHub description:

Power-efficient MiniMax H3 runtime for 1× RTX 3090 24GB — quality-first ComfyUI profiles, power-limit tuning, memory policy and Wh/render calibration.

Recommended GitHub topics:

minimax-h3
comfyui
rtx-3090
single-gpu
video-generation
local-ai
power-efficiency
nvidia
fp8
energy-efficiency

What this project does

This repository gives you a persistent MiniMax H3 configuration for:

1× NVIDIA RTX 3090 24 GB
64–128 GB system RAM
ComfyUI
Linux preferred

The main goal is not simply:

use fewer watts

The real goal is:

use the least practical energy per completed render
while preserving the chosen quality settings

That means measuring:

Wh / render

instead of looking only at instantaneous GPU wattage.

Core idea

A lower GPU power limit does not automatically mean a cheaper render.

Example:

200 W × very long render

can use more total energy than:

280 W × much shorter render

So this runtime searches for the best efficiency point:

300 W
280 W
260 W
250 W
230 W

using the same:

prompt
seed
model
resolution
duration
steps
sampler
scheduler
CFG
workflow

Then it selects the configuration with the best:

Wh / completed render

while staying stable and within an acceptable render-time penalty.

Target hardware

Reference system:

GPU:
NVIDIA RTX 3090 24 GB

System RAM:
64 GB recommended
128 GB ideal

OS:
Linux preferred

Windows:
supported with equivalent power-limit / undervolt tooling

This repository is for a single RTX 3090.

Do not use the dual-GPU Ulysses / sequence-parallel configuration from a 2×3090 setup.

Master profile

The source of truth is:

configs/minimax_h3_1x3090_ECO_MASTER_WORKFLOW.json

It defines:

single-GPU policy
power-limit calibration
quality guardrails
memory policy
production profiles
optional Ampere-native tests
optional attention acceleration tests
thermal policy
energy benchmark procedure

The JSON is a persistent configuration profile.

It is not a fake raw ComfyUI canvas export.

Recommended repository structure

minimax-h3-rtx3090-eco/
│
├── README.md
├── LICENSE
│
├── configs/
│   └── minimax_h3_1x3090_ECO_MASTER_WORKFLOW.json
│
├── docs/
│   ├── POWER-TUNING.md
│   ├── PROFILES.md
│   ├── MEMORY.md
│   ├── TROUBLESHOOTING.md
│   └── BENCHMARKS.md
│
└── scripts/
    ├── power-300w.sh
    ├── power-280w.sh
    ├── power-260w.sh
    ├── power-250w.sh
    ├── power-230w.sh
    └── restore-power.sh

Minimal setup:

README.md
configs/minimax_h3_1x3090_ECO_MASTER_WORKFLOW.json

Quick start

Clone this repository:

git clone <THIS_REPOSITORY_URL> minimax-h3-rtx3090-eco

Recommended placement:

workspace/
├── ComfyUI/
└── minimax-h3-rtx3090-eco/

or as a machine-wide AI profile:

~/ai-config/
└── minimax-h3-rtx3090-eco/

Supervisor bootstrap prompt

Give this to the AI model that supervises your MiniMax H3 environment:

This machine uses the MiniMax H3 RTX 3090 ECO Runtime.

Before changing, launching, tuning or using MiniMax H3:

1. Read README.md in this repository.
2. Read configs/minimax_h3_1x3090_ECO_MASTER_WORKFLOW.json in full.
3. Treat the master JSON as the persistent source of truth unless I explicitly override a setting.
4. Validate the real hardware and software environment before applying changes.
5. This machine uses one RTX 3090 24 GB.
6. Do not use dual-GPU Ulysses / SP settings.
7. Keep quality-changing optimizations separate from quality-neutral power tuning.
8. Prefer GPU power-limit calibration before lowering resolution, steps or model quality.
9. Measure Wh per completed render, not watts alone.
10. Do not claim that a quantization, cache or attention replacement preserves identical quality without A/B validation.
11. Use the clean production baseline before experimental speedups.
12. If reality differs from the documented assumptions, report the mismatch before silently changing the profile.

Acknowledge with:

MINIMAX-H3: RTX 3090 ECO runtime loaded

Short version:

Follow the MiniMax H3 RTX 3090 ECO Runtime in this repository.
Read README.md and configs/minimax_h3_1x3090_ECO_MASTER_WORKFLOW.json before modifying H3 settings.
Treat it as persistent machine policy unless I explicitly override it.

Global vs project-local use

Global machine profile

Recommended:

~/ai-config/minimax-h3-rtx3090-eco/

Use this when the same machine is always used for MiniMax H3.

The supervisor should treat the hardware and power policy as the machine default.

Project-local profile

Example:

project/
└── .ai/
    └── minimax-h3-rtx3090-eco/

Use this when a project needs its own:

default profile
resolution
duration
prompt style
quality requirements
experimental flags

Precedence:

explicit user request
↓
project-local override
↓
global MiniMax H3 ECO profile
↓
upstream default

Hardware reality always wins.

Single-GPU policy

This profile is intentionally different from a 2×3090 setup.

Use:

1 GPU
world_size = 1
native single-GPU H3 workflow
stock VAEDecode

Do not use:

MiniMaxH3SPUNETLoader
world_size = 2
MINIMAX_SP_DEVICES=0,1
dual-GPU Ulysses sequence parallel

There is no benefit in carrying dual-GPU orchestration into a one-GPU runtime.

Launch policy

Default:

python3 main.py --listen 0.0.0.0 --port 8188

Optional allocator configuration:

PYTORCH_CUDA_ALLOC_CONF=expandable_segments:True \
python3 main.py --listen 0.0.0.0 --port 8188

Do not add memory workarounds blindly.

Avoid as defaults:

--lowvram
--novram
--highvram
--disable-smart-memory

For RAM-constrained systems, test separately:

--disable-pinned-memory
--fp16-intermediates

Do not make them permanent without measuring the actual machine.

Recommended power profiles

01 — ECO STRICT QUALITY

Recommended starting point:

GPU power limit:
250 W

Quality settings remain unchanged:

same model
same resolution
same steps
same sampler
same scheduler
same CFG
same seed

And:

Turbo: OFF
approximate cache: OFF
attention replacement: OFF

This is the main power-saving profile.

The goal is to lower heat and power draw without intentionally altering the generation configuration.

02 — ECO BALANCED

Use:

280 W

when 250 W increases render time too much.

This is often a good compromise between:

power
temperature
throughput
energy per render

The actual winner must be measured on the individual card.

03 — SINGLE 3090 PRODUCTION

Recommended final-render baseline:

Resolution:
1280×736 when practical

Steps:
30

Recommended range:
24–36

Sampler:
ER-SDE or SA Solver

Scheduler:
Beta

CFG:
1.0

Turbo:
OFF

Cache:
OFF

Extra attention replacement:
OFF in the strict baseline

Power:

use the calibrated winner
normally start testing in the 250–280 W range

04 — SINGLE 3090 MAX QUALITY

For important final output:

Resolution:
1280×736

Optional:
1344×768 if stable

Steps:
40

Recommended range:
35–50

Sampler:
ER-SDE

Scheduler:
Beta

CFG:
1.0

Turbo:
OFF

Approximate cache:
OFF

Extra attention patches:
OFF

Power reduction should come from GPU tuning first, not from cutting quality settings.

Power calibration

Run this once after installing the system.

Test:

300 W
280 W
260 W
250 W
230 W

Linux examples:

sudo nvidia-smi -i 0 -pl 300
sudo nvidia-smi -i 0 -pl 280
sudo nvidia-smi -i 0 -pl 260
sudo nvidia-smi -i 0 -pl 250
sudo nvidia-smi -i 0 -pl 230

Before measuring:

1 warm-up render

Then:

2 measured renders per power limit

Keep identical:

prompt
seed
input image/reference
resolution
duration
model files
sampler
scheduler
steps
CFG
workflow

Measure:

render time
average GPU power
peak GPU power
Wh / render
peak VRAM
peak system RAM
GPU temperature

Formula:

Wh/render = average_GPU_power_W × render_seconds / 3600

Choose:

lowest Wh/render
+
stable execution
+
acceptable render-time penalty

Do not simply choose the lowest wattage.

Example

Suppose:

280 W
render time = 600 seconds
average draw = 265 W

Then:

265 × 600 / 3600
= 44.2 Wh

Now:

230 W
render time = 850 seconds
average draw = 220 W

Then:

220 × 850 / 3600
= 51.9 Wh

Even though the GPU uses fewer watts at any moment, the render actually consumes more energy.

So:

280 W wins

for that workload.

Quality-neutral vs quality-changing tuning

This distinction is fundamental.

Usually quality-neutral controls

GPU power limit
stable undervolt
fan curve
thermal management
same model/settings at lower clocks

These primarily affect:

speed
power
temperature

rather than intentionally changing the diffusion configuration.

Potentially quality-changing controls

lower resolution
fewer steps
Turbo / Lightning LoRA
approximate caches
attention replacements
different diffusion quantization
different text-encoder quantization

These must be A/B tested.

Do not describe them as "free performance".

Optional undervolt

Undervolting can reduce:

power
heat
fan noise

but every RTX 3090 is different.

Policy:

undervolt = optional
default = OFF

Only use a curve that has already passed stability testing.

Any:

CUDA error
render crash
artifact
driver reset

invalidates the undervolt.

Do not sacrifice stability to save a few watts.

Memory policy

For 128 GB RAM:

start with normal ComfyUI memory management

Do not automatically use:

--disable-pinned-memory

Test it only when you see:

host RAM increasing between renders
pinned memory not being released
unexpected host-memory OOM
CUDA allocation problems despite free-looking VRAM

For lower-RAM machines, memory-saving flags may become more useful.

Disk cache:

OFF by default

Use system RAM before repeatedly writing large temporary caches to SSD.

Optional Ampere-native optimization test

RTX 3090 is an Ampere GPU.

Some MiniMax H3 builds or community workflows may offer:

INT8
ConvRot
other Ampere-native paths

These may improve throughput and total Wh/render.

However:

different quantization is not guaranteed to reproduce the same output.

Therefore this belongs to an experimental profile.

A/B test:

current reference model
vs
candidate INT8 / ConvRot model

Keep:

same prompt
same seed
same resolution
same duration
same steps
same sampler

Compare both:

quality
speed
Wh/render

Only promote it to the default when the result is acceptable.

Optional attention acceleration test

Do not enable an alternative attention implementation by default.

Examples:

SageAttention
other optimized attention backends

These may help on some versions/workloads and do little on others.

Policy:

baseline first
A/B second
persistent default last

Reject the optimization if it materially changes:

detail
motion
prompt adherence
seed behavior
stability

Strict quality guardrails

In strict quality mode:

DO NOT reduce resolution to save power.
DO NOT reduce steps to save power.
DO NOT enable Turbo to save power.
DO NOT enable approximate caches to save power.
DO NOT enable unvalidated attention replacements.

Power savings should come first from:

power limit
stable voltage/frequency tuning
thermal management
efficient execution

Thermal policy

RTX 3090 can produce substantial heat.

Preferred strategy:

lower power limit
+
good airflow
+
stable fan behavior

instead of:

overclock

Monitor:

GPU core temperature
memory junction temperature

Avoid sustained thermal throttling.

The objective is:

stable long renders

not a short benchmark peak.

Supervisor operating contract

The AI supervisor should use this sequence.

Before first use

Read:

README.md
configs/minimax_h3_1x3090_ECO_MASTER_WORKFLOW.json

Then verify:

GPU count = 1
GPU model = RTX 3090
VRAM ≈ 24 GB
system RAM
ComfyUI location
installed H3 nodes
available model files

If the machine differs materially:

report it

before applying the persistent profile.

Before a render

Choose one:

ECO_STRICT_QUALITY
ECO_BALANCED
SINGLE_3090_PRODUCTION
SINGLE_3090_MAX_QUALITY
optional experimental profile

Do not permanently modify the global profile for a one-off request.

During a render

Monitor where possible:

GPU power
GPU temperature
VRAM
system RAM
render time
errors

After a render

For final-quality work, check:

skin
faces
hands
teeth / mouth
motion
flicker
prompt adherence
lip sync
audio artifacts

If the result is wrong:

change one major variable at a time

Persistent change policy

Temporary:

"use 300 W for this render"

does not mean:

change global default to 300 W

Persistent defaults should change only after:

A/B validation
energy benchmark
stability validation
quality review

Then update the master JSON.

Suggested supervisor state

Example:

{
  "runtime_loaded": true,
  "hardware_verified": true,
  "gpu_count": 1,
  "gpu_model": "RTX 3090",
  "vram_gb": 24,
  "active_profile": "01_ECO_STRICT_QUALITY",
  "power_limit_w": 250,
  "multi_gpu": false,
  "turbo": false,
  "approximate_cache": false,
  "persistent_override": false
}

The master JSON remains canonical.

Stop conditions

The supervisor should stop and report instead of silently guessing when:

the detected GPU is not an RTX 3090
VRAM is materially different
the expected H3 model is missing
the workflow unexpectedly requires multi-GPU nodes
repeated OOM occurs
the card becomes thermally unstable
an undervolt produces CUDA errors
a quantized model is substituted for the reference without approval
an optimization changes quality materially

Troubleshooting order

Use:

1. verify GPU visibility
2. verify model files
3. verify custom nodes
4. verify workflow
5. verify VRAM
6. disable experimental optimizations
7. test clean baseline
8. verify power limit
9. inspect host RAM
10. test memory workarounds

Do not begin troubleshooting by turning on more optimization plugins.

Recommended user workflow

1. Clone repository.
2. Put it next to your ComfyUI installation or in your AI-config folder.
3. Tell your supervisor agent to load it.
4. Supervisor reads README + master JSON.
5. Supervisor verifies the RTX 3090 environment.
6. Run the power sweep once.
7. Save the best Wh/render setting.
8. Use that as the persistent machine default.
9. Generate normally.
10. A/B test experimental optimizations separately.

Example:

User:
Generate the final clip at maximum quality but keep power consumption low.

Supervisor:
→ loads runtime
→ selects SINGLE_3090_MAX_QUALITY
→ uses calibrated power limit
→ preserves resolution/steps/model
→ Turbo OFF
→ cache OFF
→ render

Recommended daily configuration

Start here:

GPU power limit:
250 W

if too slow:
260–280 W

With:

base/reference H3 model
same quality settings
Turbo OFF
cache OFF
unvalidated attention replacement OFF

Then select the real long-term default from measured:

Wh/render

not instinct.

Core rules

The supervisor should retain these rules:

1. Watts are not the same as energy per render.

2. Measure Wh/render.

3. Power-limit tuning comes before quality reduction.

4. Keep strict-quality and experimental optimization separate.

5. Same-seed A/B testing is required before persistent performance changes.

6. Do not use dual-GPU SP settings on one GPU.

7. Do not claim equal quality after changing quantization without validation.

8. Stable undervolt is allowed; unstable undervolt is not an optimization.

9. Keep a clean known-good baseline.

10. Persist only proven improvements.

Philosophy

This project prioritizes:

quality
energy efficiency
stability
lower heat
reproducibility
measured optimization

The rule is:

Reduce wasted power before reducing quality.

And:

Optimize Wh per finished render, not just watts on the GPU monitor.

Disclaimer

This is a community runtime/configuration project.

It is not affiliated with or endorsed by MiniMax, ComfyUI or NVIDIA.

Hardware varies.

Upstream H3 models, ComfyUI versions and custom nodes may change.

Persistent defaults should always be revalidated after major upstream changes.
