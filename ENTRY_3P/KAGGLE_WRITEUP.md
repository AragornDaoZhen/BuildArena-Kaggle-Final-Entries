# MinimumCostOrbitalOpposedPair

*Three blocks, one scored objective, and a deliberately literal reading of the current BuildArena S01 metric.*

## Track

- Build with Agent
- Autopilot

## Run Summary

`MinimumCostOrbitalOpposedPair` is a 3-block machine composed of one Starting Block and two Steering Thrusters. The raw structure was generated entirely through the BuildArena 2.0 MCP by Codex Desktop using GPT-5.6 Luna in Autopilot mode. After the raw machine was saved, I tuned only vanilla-legal non-geometric controls and parameters, then piloted the machine manually for the submitted trajectory.

The submitted run is the first part of the linked video. In an independent trajectory analysis, the machine completed three orbital cycles in approximately **81.62 s**, ended the recording at approximately **3.19 cumulative cycles**, and retained **100% structural integrity**. These are diagnostic values computed from the submitted CSV, not an official BuildArena score.

The design is intentionally minimal: no fuel tank, no booster, no dedicated reaction wheel, no structural frame, and no redundant actuator pair. The Starting Block is the entire structural core.

## Design Rationale: Treating the Scorer as the Engineering Specification

The central design decision came from reading the current S01 quantitative objective literally rather than assuming that a visually conventional passive orbit was necessarily optimal.

The current performance metric is:

```text
PerformanceScore = 100 * (
    0.70 * OrbitProgress
  + 0.20 * SpeedScore
  + 0.10 * StructureIntegrity
)
```

Orbit Progress reaches full credit at three complete cycles, while Speed Score explicitly rewards angular progress per unit valid flight time. Structure Integrity rewards keeping the original machine connected. The final score then applies the cost penalty and the Autopilot coefficient.

This suggested a compact engineering objective:

1. reach three valid cycles;
2. accumulate angular progress as quickly as practical;
3. remain structurally intact;
4. minimize machine cost and unnecessary hardware.

The important point for us was what is **not** a separate term in the current performance formula: there is no standalone score for passive coasting, circularity, low control activity, or conventional-looking orbital insertion. Those properties may be useful means to an end, but they are not themselves the numerical objective.

Several public run videos visible during the competition appeared to emphasize obtaining a stable, conventional-looking orbit and then reducing control input. That is a natural interpretation of an orbital mission. Our approach was intentionally different: once we noticed the explicit Speed Score, we continued treating **angular rate itself as a controlled engineering variable** rather than treating orbit insertion as the endpoint.

This is not a claim about every other entry, and it is not a claim that stability is useless. It is simply the optimization choice we tested under the published metric.

## From “Orbit as a State” to “Angular Progress as a Controlled Variable”

The machine therefore does not try to behave like a passive spacecraft that receives an impulse and then coasts for a long time. It uses sustained thrust and active manual steering during the scored interval.

A useful conceptual model is a thrust-supported non-Keplerian orbit. For an ideal circular passive orbit,

```text
omega_K^2 * r = mu / r^2
```

If an additional inward acceleration `a_T` is supplied by thrust, the radial balance becomes conceptually:

```text
omega^2 * r = mu / r^2 + a_T
```

so the attainable angular rate can exceed the local passive Keplerian value:

```text
omega > omega_K
```

The actual Besiege trajectory is not an ideal analytical circle, so this equation is an interpretation rather than a fitted physical model of the recorded run. Its purpose is to explain the control philosophy: continuous thrust can contribute not only to “getting into orbit,” but also to supporting a faster angular trajectory around the scoring center.

That reframing changed the problem from “build a tiny rocket that eventually settles into orbit” to “build the smallest controllable device that can generate valid angular progress rapidly enough for three scored cycles.”

## Architecture

The raw machine contains exactly:

- 1 Starting Block;
- 2 Steering Thrusters mounted as a mirrored bilateral pair.

The two Steering Thrusters expose the same opposed thrust axis. In the raw geometry, equal actuation can provide collective force while differential actuation produces a steering moment because the two actuators are placed on opposite sides of the Starting Block.

This dual use of one actuator family is the key cost-saving idea. The same two parts serve as both propulsion and attitude-control hardware. Removing either thruster would eliminate the bilateral differential control mechanism; adding another pair would improve control authority but would also increase block cost and redundancy.

The submitted tuned BSG uses only legal vanilla UI control/parameter changes after the raw save. The submitted tuned BSG is an equivalent reconstruction of the vanilla UI settings used for the recorded run. No block was added, removed, moved, rotated, resized, or reconnected during tuning.

## Why the 3-Block and 5-Block Entries Both Exist

The 3-block machine was not the only useful solution produced in this study. An earlier Autopilot run with the **same GPT-5.6 Luna model, the same Codex Desktop environment, and the same BuildArena MCP** produced the separate 5-block `Minimal Orbital Thrustcraft` entry.

That makes the two submissions a useful small controlled comparison. They occupy different points in the performance–cost design space:

| Entry | Total blocks | Steering Thrusters | 3-cycle time (shadow analysis) | Full-run angular rate (shadow analysis) | Integrity |
|---|---:|---:|---:|---:|---:|
| MinimumCostOrbitalOpposedPair | 3 | 2 | ~81.62 s | ~0.2319 rad/s | 100% |
| Minimal Orbital Thrustcraft | 5 | 4 | ~73.31 s | ~0.2398 rad/s | 100% |

The 5-block design is faster in the recorded trajectories; the 3-block design removes two actuators and reduces total block count by 40%. Because the final cost calculation, token cleaning details, and `omega_ref` are resolved by the unified scorer, neither architecture is scientifically redundant before judging. They represent two distinct Pareto-style choices: more control/performance margin versus more aggressive mechanical minimalism.

This is why both entries were retained rather than choosing one solely by visual impression.

## Prompt Evolution as an Engineering Variable

The comparison is also interesting from an agent-design perspective.

The 5-block machine came from a relatively broad prompt asking GPT-5.6 Luna for the lowest-cost *practical* symmetric orbital machine with enough controllability for manual flight. The model selected four Steering Thrusters, preserving a more conventional amount of control authority.

The later 3-block prompt constrained the engineering question more aggressively. In particular, it told the model to:

- focus on fuel-free opposed-thrust actuators that could serve both propulsion and attitude control;
- minimize actuator count aggressively;
- accept bilateral symmetry and one principal differential steering axis;
- avoid rejecting a geometry merely because the actuator's **default** parameter value appeared weak, because legal non-geometric tuning would occur after the raw save;
- compare the minimum opposed pair with larger redundant arrangements and choose the pair unless a geometric reason made it impossible.

The full prompt and tool interaction are preserved in `chat_transcript.md`.

This means the 3-block result should not be described as an unconstrained autonomous discovery. The human prompt deliberately shaped the search space. What is interesting is that **holding the model and tool environment constant while changing the engineering priors changed the generated architecture from four actuators to two**.

For us, that is one of the most useful observations from the competition: prompt language is not merely a natural-language wrapper around an engineering optimizer. It changes which physical assumptions the model treats as fixed, which redundancies it preserves, and which designs it is willing to consider viable.

## Video

YouTube: https://youtu.be/_L0lnTcVXOg

Bilibili mirror: https://www.bilibili.com/video/BV1o3YL6dEWy/

- **Part I:** 3-block `MinimumCostOrbitalOpposedPair`
- **Part II:** 5-block `Minimal Orbital Thrustcraft`

The two runs are independent recordings concatenated only for presentation.

## LLM / Agent Setup

- LLM/model: **GPT-5.6 Luna**
- Agent environment: **Codex Desktop**
- Reasoning effort: max
- Build mode: **Autopilot**
- Agent structure: single-agent
- Visual feedback used by the build agent: no
- Structural construction interface: BuildArena 2.0 MCP
- Flight: human manual piloting after the raw build and legal tuning

The build was run in an isolated competition workspace. The submitted transcript includes the workspace instructions, exact human initial prompt, visible Codex messages, and BuildArena MCP construction activity.

## Prompts and Workflow

- Agent system/workspace instructions: included in full in `chat_transcript.md`.
- Human initial prompt: included in full in `chat_transcript.md`.
- How it was used: Codex Desktop agent controlling the BuildArena MCP.

The prompt was intentionally detailed because Autopilot allows the strategy to be specified before Build Start. After Build Start, no additional human-language steering was provided before the raw machine was saved. All structural construction was performed through the BuildArena MCP.

After the raw save, I configured legal vanilla controls/non-geometric parameters and manually piloted the machine while the official BuildArena tracker recorded the submitted trajectory.

## Code and Tools

- BuildArena 2.0 MCP
- Codex Desktop
- GPT-5.6 Luna
- Besiege: The Broken Beyond
- Official BuildArena trajectory tracker

No external scripted flight controller or automated input system was used for the submitted run.

## Notes

The submitted `machine_tuned.bsg` was re-saved after the recorded run by restoring the same vanilla UI control bindings and non-geometric parameter settings visible in the original recording. It is therefore an equivalent reconstruction of the tuned configuration used for the run rather than the original pre-run tuned save. Structural geometry was not changed.

The main submitted 3P(this 3 point machine) trajectory is the `142309` run. A separate `141852` run using the same tuned configuration was retained privately as backup evidence and is not part of this submission.（141852只是更稳定的飞行记录，依然很极端，但速度相对慢）

我是从微信公众号差评首次注意到本比赛，而我曾经在besiege做过《三体》水滴的极端机型，同时在我的工作与生活中有大量地使用ai agent，所以我认为我不太应该错过这个比赛。

我和AI都注意到比賽的overview裏的評分明確提到的對速度的追求 https://www.kaggle.com/competitions/build-arena-human-ai-colleberation-engineering-challenge/overview ，而且我注意到discussion其中的一个提问是关于轨道的合法性，官方明确回答：“For BuildArena S01, orbital progress and the corresponding score are determined from the recorded trajectory data only. So a machine does not need to satisfy an additional requirement such as having a minimal altitude, remaining outside the atmosphere, or showing a non-red orbit prediction.” 所以我在一开始就尽可能追求速度而不论是使用什么轨道，在非常多的失败飞行尝试后才成功。对于我所飞的轨道，我戏称为“高角速度推力支撑非开普勒轨道”。
我还注意到 https://build-arena.github.io/ConstructionChallenge/ 这个io页面里有另一套计分规则，其却明确提到对轨道稳定性的追求。对于这种矛盾，我认为kaggle的计分规则更正式，所以选择以kaggle的计分规则为准。

视频呈现了3 point 和 5 point 机型的正式参赛飞行，前者是极限的机型，它确实能完成3圈以上，但也只能完成3-5圈，因为之后会出现严重偏转而难以纠正；后者是全面高性能的机型，它更快完成3圈，全方向转向迅速，且有极高的加速度，实际上可以用任何种类的轨道完成无限圈的飞行。

