<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. Abstract</a></li>
<li><a href="#sec-2">2. Introduction</a></li>
</ul>
</div>
</div>

# Abstract<a id="sec-1" name="sec-1"></a>

-   The next challenge of game AI lies in Real Time Strategy (RTS) games.

*游戏AI的下一个战场在于实时策略游戏*
-   RTS games provide partially observable gaming environments, where agents interact with one another in an action space much larger than that of GO.

*RTS游戏提供了部分地观察视野，当代理机器人与其他机器人互动在行动空间中，此机器人的活动空间多于GO*
-   Mastering RTS games requires both strong macro strategies and delicate micro level execution.

*操作RTS游戏要求不仅具有强大的宏观策略，而且具有熟练而细微的水平操作*
-   Recently, great progress has been made in micro level execution, while complete solutions for macro strategies are still lacking.

-   In this paper, we propose a novel learning-based Hierarchical Macro Strategy model for mastering MOBA games, a sub-genre of RTS games.
-   Trained by the Hierarchical Macro Strategy model, agents explicitly make macro strategy decisions and further guide their micro level execution.
-   Moreover, each of the agents makes independent strategy decisions, while simultaneously communicating with the allies through leveraging a novel imitated crossagent communication mechanism.
-   We perform comprehensive evaluations on a popular 5v5 Multiplayer Online Battle Arena (MOBA) game.
-   Our 5-AI team achieves a 48% winning rate against human player teams which are ranked top 1% in the player ranking system.

# Introduction<a id="sec-2" name="sec-2"></a>

-   Light has been shed on artificial general intelligence after AlphaGo defeated world GO champion Lee Seedol (Silver et al. 2016).
-   Since then, game AI has drawn unprecedented attention from not only researchers but also the public.
-   Game AI aims much more than robots playing games.
-   Rather, games provide ideal environments that simulate the real world.
-   AI researchers can conduct experiments in games, and transfer successful AI ability to the real world.
-   Although AlphaGo is a milestone to the goal of general AI, the class of problems it represents is still simple compared to the real world.
-   Therefore, recently researchers have put much attention to real time strategy (RTS) games such as Defense of the Ancients (Dota) (OpenAI 2018a) and StarCraft (Vinyals et al. 2017; Tian et al. 2017), which represents a class of problems with next level complexity.
-   Dota is a famous set of science fiction 5v5 Multiplayer Online Battle Arena (MOBA) games.
-   Each player controls one unit and cooperate with four allies to defend allies’ turrets, attack enemies’ turrets, collect resources by killing creeps, etc. The goal is to destroy enemies’ base.
-   There are four major aspects that make RTS games much more difficult compared to GO:
-   1) Computational complexity.
-   The computational complexity in terms of action space or state space of RTS games can be up to 1020,000, while the complexity of GO is about 10250 (OpenAI 2018b).
-   2) Multi-agent.
-   Playing RTS games usually involves multiple agents. It is crucial for multiple agents to coordinate and cooporate.
-   3) Imperfect information.
-   Different to GO, many RTS games make use of fog of war (Vinyals et al. 2017) to increase game uncertainty.
-   When the game map is not fully observable, it is essential to consider gaming among one another.
-   4) Sparse and delayed rewards.
-   Learning upon game rewards in GO is challenging because the rewards are usually sparse and delayed.
-   RTS game length could often be larger than 20,000 frames, while each GO game is usually no more than 361 steps.
-   To master RTS games, players need to have strong skills in both macro strategy operation and micro level execution.
-   In recent study, much attention and attempts have been put to micro level execution (Vinyals et al. 2017; Tian et al. 2017; Synnaeve and Bessiere 2011; Wender and Watson 2012).
-   So far, Dota2 AI developed by OpenAI using reinforcement learning, i.e., OpenAI Five, has made the most advanced progress (OpenAI 2018a).
-   OpenAI Five was trained directly on micro level action space using proximal policy optimization algorithms along with team rewards (Schulman et al. 2017).
-   OpenAI Five has shown strong teamfights skills and coordination comparable to top professional Dota2 teams during a demonstration match held in The International 2018 (DOTA2 2018).
-   OpenAI’s approach did not explicitly model macro strategy and tried to learn the entire game using micro level play.
-   However, OpenAI Five was not able to defeat professional teams due to weakness in macro strategy management (Vincent 2018; Simonite 2018).
-   Related work has also been done in explicit macro strategy operation, mostly focused on navigation.
-   Navigation aims to provide reasonable destination spots and efficient routes for agents.
-   Most related work in navigation used influence maps or potential fields (DeLoura 2001; Hagelbäck and Johansson 2008; do Nascimento Silva and Chaimowicz 2015).
-   Influence maps quantify units using handcrafted equations.
-   Then, multiple influence maps are fused using rules to provide a single-value output to navigate agents.
-   Providing destination is the most important purpose of navigation in terms of macro strategy operation.
-   The ability to get to the right spots at right time makes essential difference between high level players and the others.
-   Planning has also been used in macro strategy operation.
-   Ontanon et al. proposed Adversarial Hierarchical-Task Network (AHTN) Planning (Ontanón and Buro 2015) to search hierarchical tasks in RTS game playing.
-   Although AHTN shows promising results in a mini-RTS game, it suffers from efficiency issue which makes it difficult to apply to full MOBA games directly.
-   Despite of the rich and promising literature, previous work in macro strategy failed to provide complete solution:
-   First, reasoning macro strategy implicitly by learning upon micro level action space may be too difficult.
-   OpenAI Five’s ability gap between micro level execution and macro strategy operation was obvious.
-   It might be over-optimistic to leave models to figure out high level strategies by simply looking at micro level actions and rewards.
-   We consider explicit macro strategy level modeling to be necessary.
-   Second, previous work on explicit macro strategy heavily relied on handcrafted equations for influence maps/potential fields computation and fusion.
-   In practice, there are usually thousands of numerical parameters to manually decide, which makes it nearly impossible to achieve good performance.
-   Planning methods on the other hand cannot meet efficiency requirement of full MOBA games.
-   Third, one of the most challenging problems in RTS game macro strategy operation is coordination among multiple agents.
-   Nevertheless, to the best of our knowledge, previous work did not consider it in an explicit way.
-   OpenAI Five considers multi-agent coordination using team rewards on micro level modeling.
-   However, each agent of OpenAI Five makes decision without being aware of allies’ macro strategy decisions, making it difficult to develop top coordination ability in macro strategy level.
-   Finally, we have found that modeling strategic phase is crucial for MOBA game AI performance.
-   However, to the best of our knowledge, previous work did not consider this.
-   Teaching agents to learn macro strategy operation, however, is challenging.
-   Mathematically defining macro strategy, e.g., besiege and split push, is difficult in the first place.
-   Also, incorporating macro strategy on top of OpenAI Five’s reinforcement learning framework (OpenAI 2018a) requires corresponding execution to gain rewards, while macro strategy execution is a complex ability to learn by itself.
-   Therefore, we consider supervised learning to be a better scheme because high quality game replays can be fully leveraged to learn macro strategy along with corresponding execution samples.
-   Note that macro strategy and execution learned using supervised learning can further act as an initial policy for reinforcement learning.
-   In this paper, we propose Hierarchical Macro Strategy (HMS) model - a general supervised learning framework for MOBA games such as Dota.
-   HMS directly tackles with computational complexity and multi-agent challenges of MOBA games.
-   More specifically, HMS is a hierarchical model which conducts macro strategy operation by predicting attention on the game map under guidance of game phase modeling.
-   Thereby, HMS reduces computational complexity by incorporating game knowledge.
-   Moreover, each HMS agent conducts learning with a novel mechanism of communication with teammates agents to cope with multi-agent challenge.
-   Finally, we have conducted extensive experiments in a popular MOBA game to evaluate our AI ability.
-   We matched with hundreds of human player teams that ranked above 99% of players in the ranked system and achieved 48% winning rate.
-   The rest of this paper is organized as follows:
-   First, we briefly introduce Multiplayer Online Battle Arena (MOBA) games and compare the computational complexity with GO.
-   Second, we illustrate our proposed Hierarchical Macro Strategy model.
-   Then, we present experimental results in the fourth section.
-   Finally, we conclude and discuss future work.