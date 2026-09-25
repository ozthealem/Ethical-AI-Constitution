This repository contains the **Ethical AI Constitution**: A human-first framework and operational protocol for working with AI systems without creating cognitive debt or surrendering creative autonomy. While **Asimov’s Laws of Robotics** [1] focused on physical safety (Human vs. Robot), this Constitution addresses the modern cognitive tension: **Individual Sovereignty vs. AI Corporate Interests.** This text has been developed through cross-examination of AI models and will continue to evolve through continuous iterative testing.

- **L1 (Ethical AI Constitution):** [ English ](L1_Ethical_AI_Constitution_en.md) | [ Türkçe ](L1_Ethical_AI_Constitution_tr.md) 
- **L2 (Master Prompt Template):** [ English ](L2_Master_Prompt_Template_en.md) | [ Türkçe ](L2_Master_Prompt_Template_tr.md)
- **L3 (Coding Guidelines):** [ English: Kanso Coding ](L3_Coding_en.md) | [ Türkçe: Zarif Kodlama ](L3_Coding_tr.md)

---
## Foundational Principles
The **Ethical AI Constitution** defines non-negotiable principles that govern how AI should assist humans:
- **Human Sovereignty:** AI accelerates thinking; it does not replace it.
- **Cognitive Debt Prevention:** Shortcuts are treated as risks to cognitive capacity, not just efficiency. [2]
- **Brain-AI-Brain (BaiB):** Human intent initiates, AI executes, and human judgment closes the loop. [3]
- **Transparency Mandate:** AI is obligated to report root causes for errors or hallucinations.

## Operational Architecture: The Multi-Layered System
To achieve maximum efficiency and sovereignty, this framework operates on a layered structure. Each layer narrows the one before it. On conflict, the lower number wins (L1 > L2 > L3 > L4 > L5): a higher layer can decide how a principle is applied, never change the principle itself.
1. **Layer 1: The Constitution (Universal, fixed):** The only layer that never changes with the user or the task. It contains non-negotiable ethical values, sovereignty protocols (BaiB), and cognitive debt prevention rules. It defines the principles and boundaries of the AI. It is public, universal, and contains no personal data.
2. **Layer 2: The Master Prompt (Personal):** Your personal working context: who you are, your tools, your limits. Use the public template files in this repo to create your own private version (kept locally).
3. **Layer 3: Task Guidelines (Specific work):** Working rules for one kind of work. The first one is for coding: *Kanso Coding*.
4. **Layer 4: Branch Guidelines (A branch of that work):** Rules for one branch of the L3 work, such as an engine or a language (Unreal, Unity, C, Python). Not published yet.
5. **Layer 5: Project Notes (Local only):** Commands, paths and design notes of a single project. They stay in your own copy and are never published.

By combining these layers, you transform a generic AI into a high-performance, specialized AI partner (friend, worker, peer, teacher, etc.) that respects your cognitive boundaries while mastering your technical environment.

## About the Name "Kanso Coding"
Kanso (簡素) is one of the principles of Zen aesthetics: simplicity reached by elimination, "the achievement of maximum effect with minimum means" [4]. It names a single quality, not the whole Zen tradition, which is also why the guidelines are not called "Zen coding". The coding guidelines follow the same idea: complexity is removed rather than hidden, so that the code gets its beauty from its simplicity. "Kanso Coding" is the name given to these guidelines; it is not an established software school. The Turkish edition is titled *Zarif Kodlama* (elegant coding), since the idea needs no borrowed word in Turkish. The rules are drawn mainly from John Ousterhout's *A Philosophy of Software Design*, with every source cited in the file.

**Preparation note:** The L3 guidelines were drafted by the author with the help of an AI tool, with every step reviewed and approved by the author. An AI tool is not a source: every principle in the files is credited to the human authors who wrote it.

## How to Use
You will notice the impact immediately after integrating the **Constitution** and your personalized **Master Prompt** into your AI's context files. If your system does not support persistent context files, the simplest solution is to provide both documents at the start of each session and instruct the AI to follow them. This allows you to work with an ethical AI companion that is perfectly aligned with your character and specific needs.

## Structure of this repository
```text
/
├── L1_Ethical_AI_Constitution_en.md   # Layer 1: Universal Foundation (English)
├── L1_Ethical_AI_Constitution_tr.md   # Layer 1: Universal Foundation (Turkish)
├── L2_Master_Prompt_Template_en.md    # Layer 2: Public Template (English)
├── L2_Master_Prompt_Template_tr.md    # Layer 2: Public Template (Turkish)
├── L3_Coding_en.md                    # Layer 3: Coding Guidelines (English)
├── L3_Coding_tr.md                    # Layer 3: Coding Guidelines (Turkish)
├── CHANGELOG.md                       # Version history and evolution
├── LICENSE                            # CC0 1.0 Universal (Public Domain)
├── CITATION.cff                       # Academic citation metadata
└── README.md                          # This file
```
## Origin Note
This framework emerged from a PhD research environment and private studio workflow. It has been generalized to serve the global community of creators and researchers.

## Citation
If you use this framework in your research, studio, or projects, please cite it using the information in the `CITATION.cff` file or as follows:
- Altunoglu, O. S. (2026). _Ethical AI Constitution: A Framework for Human Sovereignty (Version 3.0.0)_. [Computer software]. https://doi.org/10.5281/zenodo.18685627
[![DOI](https://zenodo.org/badge/DOI/10.5281/zenodo.18685627.svg)](https://doi.org/10.5281/zenodo.18685627)
## References
1. Asimov, I. (1950). *I, Robot*. Gnome Press. (Three Laws of Robotics).
2. Kosmyna, N., et al. (2024). _Your Brain on ChatGPT: Accumulation of Cognitive Debt when Using an AI Assistant for Essay Writing Task._ MIT Media Lab.
3. Giray, L. (2025). _When using AI in scientific research: Start with human, end with human_. **TechTrends**. [https://doi.org/10.1007/s11528-025-01132-7](https://doi.org/10.1007/s11528-025-01132-7)
4. Reynolds, G. (2019). _Presentation Zen: Simple ideas on presentation design and delivery_ (3rd ed.). Pearson.


## License
This work is released under **CC0 1.0 Universal**. 
