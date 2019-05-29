<div id="table-of-contents">
<h2>Table of Contents</h2>
<div id="text-table-of-contents">
<ul>
<li><a href="#sec-1">1. Abstract</a></li>
<li><a href="#sec-2">2. Introduction</a></li>
<li><a href="#sec-3">3. Multiplayer Online Battle Arena (MOBA) Games</a>
<ul>
<li><a href="#sec-3-1">3.1. Game Description</a></li>
<li><a href="#sec-3-2">3.2. Computational Complexity</a></li>
<li><a href="#sec-3-3">3.3. MOBA AI Macro Strategy Architecture</a></li>
</ul>
</li>
<li><a href="#sec-4">4. Hierarchical Macro Strategy Model</a>
<ul>
<li><a href="#sec-4-1">4.1. Model Overview</a></li>
<li><a href="#sec-4-2">4.2. Attention Layer</a></li>
<li><a href="#sec-4-3">4.3. Phase layer</a></li>
<li><a href="#sec-4-4">4.4. Imitated Cross-agents Communication</a></li>
</ul>
</li>
<li><a href="#sec-5">5. Experiments</a>
<ul>
<li><a href="#sec-5-1">5.1. Experimental Setup</a>
<ul>
<li><a href="#sec-5-1-1">5.1.1. Data Preparation</a></li>
<li><a href="#sec-5-1-2">5.1.2. Model Setup</a></li>
</ul>
</li>
<li><a href="#sec-5-2">5.2. Experimental Results</a>
<ul>
<li><a href="#sec-5-2-1">5.2.1. Opening Attention</a></li>
<li><a href="#sec-5-2-2">5.2.2. Attention Distribution Affected by Phase Layer</a></li>
<li><a href="#sec-5-2-3">5.2.3. Macro Strategy Embedding</a></li>
<li><a href="#sec-5-2-4">5.2.4. Match against Human Players</a></li>
<li><a href="#sec-5-2-5">5.2.5. Imitated Cross-agents Communication</a></li>
<li><a href="#sec-5-2-6">5.2.6. Phase layer</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#sec-6">6. Conclusion and Future Work</a></li>
<li><a href="#sec-7">7. References</a></li>
</ul>
</div>
</div>

# Abstract<a id="sec-1" name="sec-1"></a>

-   The next challenge of game AI lies in Real Time Strategy (RTS) games.

*游戏AI的下一个战场在于实时策略游戏*
-   RTS games provide partially observable gaming environments, where agents interact with one another in an action space much larger than that of GO.

*RTS游戏提供了部分地观察视野，当代理与其他代理互动在行动空间中，此代理的活动空间多于GO*
-   Mastering RTS games requires both strong macro strategies and delicate micro level execution.

*操作RTS游戏要求不仅具有强大的宏观策略，而且具有熟练地微观层面操作*
-   Recently, great progress has been made in micro level execution, while complete solutions for macro strategies are still lacking.

*近期，微观层面操作有了巨大的进步，再此同时宏观策略的完整的解决方案仍然缺乏*
-   In this paper, we propose a novel learning-based Hierarchical Macro Strategy model for mastering MOBA games, a sub-genre of RTS games.

*在这个论文中，我们打算将新颖的基于学习分层宏观策略模型用于控制实时策略的一个领域——MOBA类游戏中*
-   Trained by the Hierarchical Macro Strategy model, agents explicitly make macro strategy decisions and further guide their micro level execution.

*通过训练这个分层宏观策略模型，代理清晰地在宏观策略中做出决定，并且更进一步地指导微观层面操作*
-   Moreover, each of the agents makes independent strategy decisions, while simultaneously communicating with the allies through leveraging a novel imitated cross-agent communication mechanism.

*此外，每一个代理都能独立的做出战略性决定，而且同时通过利用新颖的模仿反向代理交流机制与盟军进行交流*
-   We perform comprehensive evaluations on a popular 5v5 Multiplayer Online Battle Arena (MOBA) game.

*我们在流行的5v5MOBA类游戏中得出了综合的评估*
-   Our 5-AI team achieves a 48% winning rate against human player teams which are ranked top 1% in the player ranking system.

*我们的五AI队伍获得了48%的胜率在对抗人类玩家队伍，这些人类队伍在排名系统中位于1%*

# Introduction<a id="sec-2" name="sec-2"></a>

-   Light has been shed on artificial general intelligence after AlphaGo defeated world GO champion Lee Seedol (Silver et al. 2016).

*在阿尔法GO击败世界围棋冠军李·西多尔后，通用人工智能的光芒已经散去。*
-   Since then, game AI has drawn unprecedented attention from not only researchers but also the public.

*从那时起，游戏AI引起了前所未有的关注，不仅仅是研究人员，还有公众。*
-   Game AI aims much more than robots playing games.

*游戏AI目的不只只是机器人玩游戏。*
-   Rather, games provide ideal environments that simulate the real world.

*在某种程度上，游戏提供模拟现实世界的理想环境*
-   AI researchers can conduct experiments in games, and transfer successful AI ability to the real world.

*AI研究人员可以在游戏中进行实验，并且将成功的AI能力转换到现实世界。*
-   Although AlphaGo is a milestone to the goal of general AI, the class of problems it represents is still simple compared to the real world.

*尽管阿尔法GO是实现通用人工智能目标的一个里程碑，与现实世界相比，它所代表的问题类别仍然很简单。*
-   Therefore, recently researchers have put much attention to real time strategy (RTS) games such as Defense of the Ancients (Dota) (OpenAI 2018a) and StarCraft (Vinyals et al. 2017; Tian et al. 2017), which represents a class of problems with next level complexity.

*因此，最近研究者将注意力放在了实时战略游戏比如古代防御和星际争霸，它表示一类具有下一级复杂性的问题。*
-   Dota is a famous set of science fiction 5v5 Multiplayer Online Battle Arena (MOBA) games.

*古代防御是有名的一套科幻5v5多人在线竞技场游戏。*
-   Each player controls one unit and cooperate with four allies to defend allies’ turrets, attack enemies’ turrets, collect resources by killing creeps, etc. The goal is to destroy enemies’ base.

*每一个玩家都控制一个单位，与四个盟友合作将保卫同盟的塔，攻击敌方的塔，通过击杀对手来收集资源，他们的目标是摧毁敌方基地。*
-   There are four major aspects that make RTS games much more difficult compared to GO:

*相比较于GO，有四个主要方面使得实时策略游戏更加复杂：*
-   1) Computational complexity.

*可计算复杂度。*
-   The computational complexity in terms of action space or state space of RTS games can be up to 10<sup>20</sup>,000, while the complexity of GO is about 10<sup>250</sup> (OpenAI 2018b).

*在RTS游戏的状态空间和行动空间，可计算复杂度超过10<sup>20</sup>,000，而GO的复杂度大约在10<sup>250</sup>*
-   2) Multi-agent.

*多智能体。*
-   Playing RTS games usually involves multiple agents. It is crucial for multiple agents to coordinate and cooporate.

*玩RTS游戏通常涉及到多个智能体。多个智能体之间的合作与协调是非常重要的。*
-   3) Imperfect information.

*不完美信息。*
-   Different to GO, many RTS games make use of fog of war (Vinyals et al. 2017) to increase game uncertainty.

*不同于GO，许多的RTS游戏使用了战争迷雾来提升游戏的不确定性。*
-   When the game map is not fully observable, it is essential to consider gaming among one another.

*当游戏地图不是完全可观测的，考虑彼此之间的博弈是很有必要的。*
-   4) Sparse and delayed rewards.

*稀疏和延时奖励。*
-   Learning upon game rewards in GO is challenging because the rewards are usually sparse and delayed.

*在GO上学习游戏奖励是有挑战性的，因为奖赏通常是稀疏和延时的。*
-   RTS game length could often be larger than 20,000 frames, while each GO game is usually no more than 361 steps.

*RTS游戏的长度通常超过20,000帧，而游戏GO通常不到361步。*
-   To master RTS games, players need to have strong skills in both macro strategy operation and micro level execution.

*掌握RTS游戏，玩家需要有强大的技术在宏观策略操作和微观水平实施上。*
-   In recent study, much attention and attempts have been put to micro level execution (Vinyals et al. 2017; Tian et al. 2017; Synnaeve and Bessiere 2011; Wender and Watson 2012).

*在近期研究中，更多的关注和测试都是放在微观水平实施当中。*
-   So far, Dota2 AI developed by OpenAI using reinforcement learning, i.e., OpenAI Five, has made the most advanced progress (OpenAI 2018a).

*至今为止，基于增强学习的OPENAi开发的DOTA2的AI有了更多的进步。*
-   OpenAI Five was trained directly on micro level action space using proximal policy optimization algorithms along with team rewards (Schulman et al. 2017).

*OPENAIFive被直接训练在微观水平行动空间，用了近端策略优化算法随同团队奖赏。*
-   OpenAI Five has shown strong teamfights skills and coordination comparable to top professional Dota2 teams during a demonstration match held in The International 2018 (DOTA2 2018).

*在国际2018年举行的示范赛中，OPENAIFive表现出了与顶级专业DOTA2团队相当的强大团队战斗技能和协调能力。*
-   OpenAI’s approach did not explicitly model macro strategy and tried to learn the entire game using micro level play.

*OPENAI的研究没有明确地建模了宏观策略而是尝试学习使用微观水平玩整个游戏。*
-   However, OpenAI Five was not able to defeat professional teams due to weakness in macro strategy management (Vincent 2018; Simonite 2018).

*然而，OPENAIFive没有能够击败专业的队伍由于在宏观策略管理很弱。*
-   Related work has also been done in explicit macro strategy operation, mostly focused on navigation.

*在明确的宏观策略操作中，相关的工作已经完成，主要集中在导航方面。*
-   Navigation aims to provide reasonable destination spots and efficient routes for agents.

*导航的目的是为AI提供合理的目的地和有效的路线。*
-   Most related work in navigation used influence maps or potential fields (DeLoura 2001; Hagelbäck and Johansson 2008; do Nascimento Silva and Chaimowicz 2015).

*在导航中大多数相关工作使用的影响图或者势场。*
-   Influence maps quantify units using handcrafted equations.

*影响图使用手工制作的方程式量化单位。*
-   Then, multiple influence maps are fused using rules to provide a single-value output to navigate agents.

*所以，多个影响图结合规则去提供一个单一价值的输出用来导航AI。*
-   Providing destination is the most important purpose of navigation in terms of macro strategy operation.

*在一些宏观策略操作中，提供目的地是导航最重要的目的。*
-   The ability to get to the right spots at right time makes essential difference between high level players and the others.

*在正确的时间到达正确的场地的能力在高水平玩家与普通玩家当中是至关重要的。*
-   Planning has also been used in macro strategy operation. Ontanon et al.

*计划也被使用到宏观策略操作中。*
-   proposed Adversarial Hierarchical-Task Network (AHTN) Planning (Ontanón and Buro 2015) to search hierarchical tasks in RTS game playing.

*提出了在RTS游戏中对抗性分层任务网络规划用来搜索分层任务。*
-   Although AHTN shows promising results in a mini-RTS game, it suffers from efficiency issue which makes it difficult to apply to full MOBA games directly.

*尽管AHTN在迷你RTS游戏中显示出有希望的结果，但它存在效率问题，难以直接应用于完整的MOBA类游戏中。*
-   Despite of the rich and promising literature, previous work in macro strategy failed to provide complete solution:

*尽管文献丰富，前景广阔，但以往的宏观战略研究未能提供完整的解决方案。*
-   First, reasoning macro strategy implicitly by learning upon micro level action space may be too difficult.

*第一，通过学习微观水平行动空间获得隐含有意义的宏观策略可能太困难。*
-   OpenAI Five’s ability gap between micro level execution and macro strategy operation was obvious.

*OPENAIFive的能力差距在微观水平实现与宏观策略操作之间是可观测的。*
-   It might be over-optimistic to leave models to figure out high level strategies by simply looking at micro level actions and rewards.

*仅仅通过观察微观层面的行动和奖励，让模型去制定高层次的战略可能过于乐观。*
-   We consider explicit macro strategy level modeling to be necessary.

*我们认为明确的宏观战略层面建模是必要的。*
-   Second, previous work on explicit macro strategy heavily relied on handcrafted equations for influence maps/potential fields computation and fusion.

*第二，之前的显式宏观策略研究主要依赖手工制作的影响图/势场计算和融合方程。*
-   In practice, there are usually thousands of numerical parameters to manually decide, which makes it nearly impossible to achieve good performance.

*在实际应用中，通常有数千个数值参数需要人工确定，这使得实现良好性能几乎是不可能的。*
-   Planning methods on the other hand cannot meet efficiency requirement of full MOBA games.

*另一方面，规划方法也不能满足整个moba游戏的效率要求。*
-   Third, one of the most challenging problems in RTS game macro strategy operation is coordination among multiple agents.

*第三，RTS游戏宏观策略操作中最具挑战性的问题之一是多个AI之间的协调。*
-   Nevertheless, to the best of our knowledge, previous work did not consider it in an explicit way.

*然而，据我们所知，以前的工作并没有明确地考虑到这一点。*
-   OpenAI Five considers multi-agent coordination using team rewards on micro level modeling.

*OPENAIFive考虑在微观水平建模中使用团队奖励进行多AI协调。*
-   However, each agent of OpenAI Five makes decision without being aware of allies’ macro strategy decisions, making it difficult to develop top coordination ability in macro strategy level.

*然而，OPENAIFive的每一个AI都在不了解盟友宏观战略决策的情况下做出决策，使得其难以在宏观战略层面上培养最高的协调能力。*
-   Finally, we have found that modeling strategic phase is crucial for MOBA game AI performance.

*最后，我们发现建模策略阶段对Moba游戏的人工智能性能至关重要。*
-   However, to the best of our knowledge, previous work did not consider this.

*然而，据我们所知，以前的工作并没有考虑到这一点。*
-   Teaching agents to learn macro strategy operation, however, is challenging.

*然而，教AI学习宏观战略操作是一个挑战。*
-   Mathematically defining macro strategy, e.g., besiege and split push, is difficult in the first place.

*在数学上定义宏观战略，例如围攻和分裂推进，首先是困难的。*
-   Also, incorporating macro strategy on top of OpenAI Five’s reinforcement learning framework (OpenAI 2018a) requires corresponding execution to gain rewards, while macro strategy execution is a complex ability to learn by itself.

*此外，在Openai Five的强化学习框架之上结合宏策略需要相应的执行来获得奖励，而宏策略执行本身就是一种复杂的学习能力。*
-   Therefore, we consider supervised learning to be a better scheme because high quality game replays can be fully leveraged to learn macro strategy along with corresponding execution samples.

*因此，我们认为监督学习是一个更好的方案，因为高质量的游戏回放可以充分利用，学习宏观策略和相应的执行样本。*
-   Note that macro strategy and execution learned using supervised learning can further act as an initial policy for reinforcement learning.

*请注意，使用监督学习训练的宏观策略和执行可以进一步充当强化学习的初始政策。*
-   In this paper, we propose Hierarchical Macro Strategy (HMS) model - a general supervised learning framework for MOBA games such as Dota.

*在本文中，我们提出了分层宏观策略（HMS）模型 - 一种用于MOBA游戏（如Dota）的通用监督学习框架。*
-   HMS directly tackles with computational complexity and multi-agent challenges of MOBA games.

*HMS直接解决了MOBA游戏的计算复杂性和多智能体挑战。*
-   More specifically, HMS is a hierarchical model which conducts macro strategy operation by predicting attention on the game map under guidance of game phase modeling.

*更具体地，HMS是通过在游戏阶段建模的指导下预测对游戏地图的关注来进行宏观策略操作的分层模型。*
-   Thereby, HMS reduces computational complexity by incorporating game knowledge.

*因此，HMS通过结合游戏知识来降低计算复杂性。*
-   Moreover, each HMS agent conducts learning with a novel mechanism of communication with teammates agents to cope with multi-agent challenge.

*此外，每个HMSAI都通过与队友AI的新型交流机制进行学习，以应对多智能体挑战。*
-   Finally, we have conducted extensive experiments in a popular MOBA game to evaluate our AI ability.

*最后，我们在流行的MOBA游戏中进行了大量实验，以评估我们的AI能力。*
-   We matched with hundreds of human player teams that ranked above 99% of players in the ranked system and achieved 48% winning rate.

*我们与数百名人类玩家队伍相匹配，他们在排名系统中排名超过99％的玩家，并且获得了48％的胜率。*
-   The rest of this paper is organized as follows:

*本文的其余部分安排如下：*
-   First, we briefly introduce Multiplayer Online Battle Arena (MOBA) games and compare the computational complexity with GO.

*首先，我们简要介绍多人在线战斗竞技场（MOBA）游戏，并将计算复杂性与GO进行比较。*
-   Second, we illustrate our proposed Hierarchical Macro Strategy model.

*其次，我们说明了我们提出的分层宏观策略模型。*
-   Then, we present experimental results in the fourth section.

*然后，我们在第四部分介绍实验结果。*
-   Finally, we conclude and discuss future work.

*最后，我们总结并讨论未来的工作。*

# Multiplayer Online Battle Arena (MOBA) Games<a id="sec-3" name="sec-3"></a>

## Game Description<a id="sec-3-1" name="sec-3-1"></a>

-   MOBA is currently the most popular sub-genre of the RTS games.

*MOBA目前是RTS游戏中最受欢迎的子类型。*
-   MOBA games are responsible for more than 30% of the online gameplay all over the world, with titles such as Dota, League of Legends, and Honour of Kings (Murphy 2015).

*MOBA游戏占全球在线游戏玩法的30％以上，其中包括Dota，英雄联盟和王者荣耀。*
-   According to a worldwide digital games market report in February 2018, MOBA games ranked first in grossing in both PC and mobile games (SuperData 2018).

*根据2018年2月的全球数字游戏市场报告，MOBA游戏在PC和手机游戏中的销量排名第一。*
-   In MOBA, the standard game mode requires two 5-player teams play against each other.

*在MOBA中，标准游戏模式需要两个5人队伍相互比赛。*
-   Each player controls one unit, i.e., hero.

*每个玩家控制一个单位，即英雄。*
-   There are numerous of heroes in MOBA, e.g., more than 80 in Honour of Kings.

*MOBA中有许多英雄，例如，在王者荣耀中超过80。*
-   Each hero is uniquely designed with special characteristics and skills.

*每个英雄都设计独特，具有特殊的特点和技能。*
-   Players control movement and skill releasing of heroes via the game interface.

*玩家通过游戏界面控制英雄的移动和技能释放。*
-   As shown in Figure. 1a, Honour of Kings players use left bottom steer button to control movements, while right bottom set of buttons to control skills.

*如图所示。 1a，Honor of Kings玩家使用左下角转向按钮来控制动作，而右下角设置按钮来控制技能。*
-   Surroundings are observable via the main screen.

*可通过主屏幕观察周围环境。*
-   Players can also learn full map situation via the left top corner mini-map, where observable turrets, creeps, and heroes are displayed as thumbnails.

*玩家还可以通过左上角迷你地图了解完整的地图情况，其中可观察的炮塔，小兵和英雄显示为缩略图。*
-   Units are only observable either if they are allies’ units or if they are within a certain distance to allies’ units.

*只有当他们是盟友的单位或者他们与盟友的单位相距一定距离时才能观察到单位。*
-   There are three lanes of turrets for each team to defend, three turrets in each lane.

*每支队伍都有三个炮塔用于防守，每个车道有三个炮塔。*
-   There are also four jungle areas on the map, where creep resources can be collected to increase gold and experience.

*地图上还有四个丛林区域，可以收集爬行资源以增加黄金和经验。*
-   Each hero starts with minimum gold and level 1.

*每个英雄都以最低金币和等级1开始。*
-   Each team tries to leverage resources to obtain as much gold and experience as possible to purchase items and upgrade levels.

*每个团队都试图利用资源来获得尽可能多的黄金和经验，以购买物品和升级。*
-   The final goal is to destroy enemy’s base.

*最终目标是摧毁敌人的基地。*
-   A conceptual map of MOBA is shown in Figure. 1b.

*MOBA的概念图如图所示1B。*
-   To master MOBA games, players need to have both excellent macro strategy operation and proficient micro level execution.

*要掌握MOBA游戏，玩家需要具备出色的宏观策略操作和熟练的微观级别执行。*
-   Common macro strategies consist of opening, laning, ganking, ambushing, etc.

/一般的宏观策略包括开放，拦截，抓人，伏击，等等。
-   Proficient micro level execution requires high accuracy of control and deep understanding of damage and effects of skills.

*熟练的微级执行需要高精度的控制和对伤害和技能效果的深刻理解。*
-   Both macro strategy operation and micro level execution require mastery of timing to excel, which makes it extremely challenging and interesting.

*无论是宏观战略操作还是微观层面执行，都需要掌握卓越的时机，这使得它极具挑战性和趣味性。*
-   More discussion of MOBA can be found in (Silva and Chaimowicz 2017).

*有关MOBA的更多讨论可以在 (Silva and Chaimowicz 2017).*
-   Next, we will quantify the computational complexity of MOBA using Honour of Kings as an example.

*接下来，我们将使用王者荣耀来量化MOBA的计算复杂性。*

## Computational Complexity<a id="sec-3-2" name="sec-3-2"></a>

-   The normal game length of Honour of Kings is about 20 minutes, i.e., approximately 20,000 frames in terms of gamecore.

*王者荣耀的正常游戏长度约为20分钟，即游戏核心约为20,000帧。*
-   At each frame, players make decision with tens of options, including movement button with 24 directions, and a few skill buttons with corresponding releasing position/directions.

*在每个帧中，玩家通过数十个选项做出决定，包括具有24个方向的移动按钮，以及具有相应释放位置/方向的一些技能按钮。*
-   Even with significant discretization and simplification, as well as reaction time increased to 200ms, the action space is at magnitude of 101,500.

*即使具有显着的离散化和简化，以及反应时间增加到200ms，动作空间也达到101,500。*
-   As for state space, the resolution of Honour of Kings map is 130,000 by 130,000 pixels, and the diameter of each unit is 1,000.

*至于国家空间，王者荣耀地图的分辨率是130,000乘130,000像素，每个单位的直径是1,000。*
-   At each frame, each unit may have different status such as hit points, levels, gold.

*在每个框架中，每个单元可能具有不同的状态，例如生命值，等级，金币。*
-   Again, the state space is at magnitude of 1020,000 with significant simplification.

*同样，状态空间大小为1020,000，显着简化。*
-   Comparison of action space and state space between MOBA and GO is listed in Table. 1.

*表中列出了MOBA和GO之间的动作空间和状态空间的比较1.*

## MOBA AI Macro Strategy Architecture<a id="sec-3-3" name="sec-3-3"></a>

-   Our motivation of designing MOBA AI macro strategy model was inspired from how human players make strategic decisions.

*我们设计MOBA AI宏观战略模型的动机源于人类玩家如何做出战略决策。*
-   During MOBA games, experienced human players are fully aware of game phases, e.g., opening phase, laning phase, mid game phase, and late game phase (Silva and Chaimowicz 2017).

*在MOBA游戏期间，经验丰富的人类玩家完全了解游戏阶段，例如开放阶段，游戏阶段，游戏中期和游戏后期阶段。*
-   During each phase, players pay attention to the game map and make corresponding decision on where to dispatch the heroes.

*在每个阶段，玩家都会关注游戏地图，并对发送英雄的位置做出相应的决定。*
-   For example, during the laning phase players tend to focus more on their own lanes rather than backing up allies, while during mid to late phases, players pay more attention to teamfight spots and pushing enemies’ base.

/例如，在比赛阶段，玩家倾向于更多地关注他们自己的道路，而不是支持盟友，而在中期到后期阶段，玩家更多地关注团队战斗点并推动敌人的基地。
-   To sum up, we formulate the macro strategy operation process as "phase recognition -> attention prediction -> execution".

*综上所述，我们将宏观战略运作过程表述为“阶段识别 - >关注预测 - >执行”。*
-   To model this process, we propose a two-layer macro strategy architecture, i.e., phase and attention:

*为了模拟这个过程，我们提出了一个两层宏策略架构，即阶段和注意力：*
-   • Phase layer aims to recognize current game phase so that attention layer can have better sense about where to pay attention to.

*阶段层旨在识别当前的游戏阶段，以便关注层可以更好地了解在哪里注意。*
-   • Attention layer aims to predict the best region on game maps to dispatch heroes.

*注意层旨在预测游戏地图上的最佳区域以派遣英雄。*
-   Phase and Attention layers act as high level guidance for micro level execution.

*阶段和注意层充当微观水平执行的高级指导。*
-   We will describe details of modeling in the next section.

*我们将在下一节中描述建模的细节。*
-   The network structure of micro level model is almost identical to the one used in OpenAI Five1 (OpenAI 2018a), but in a supervised learning manner.

*微观模型的网络结构几乎与OpenAI Five1（OpenAI 2018a）中使用的网络结构相同，但是采用监督学习方式。*
-   We did minor modification to adapt it to Honour of Kings, such as deleting Teleport.

*我们做了一些小修改，以使其适应王者荣耀，例如删除Teleport。*

# Hierarchical Macro Strategy Model<a id="sec-4" name="sec-4"></a>

-   We propose a Hierarchical Macro Strategy (HMS) model to consider both phase and attention layers in a unified neural network.

*我们提出了一种分层宏观策略（HMS）模型来考虑统一神经网络中的相位和关注层。*
-   We will first present the unified network architecture.

*我们将首先介绍统一的网络架构。*
-   Then, we illustrate how we construct each of the phase and attention layers.

*然后，我们将说明如何构建每个阶段和关注层。*

## Model Overview<a id="sec-4-1" name="sec-4-1"></a>

-   We propose a Hierarchical Macro Strategy model (HMS) to model both attention and phase layers as a multi-task model.

*我们提出了一种分层宏观策略模型（HMS），将注意力和相位层建模为多任务模型。*
-   It takes game features as input.

*它将游戏功能作为输入。*
-   The output consists of two tasks, i.e., attention layer as the main task and phase layer as an auxiliary task.

*输出包括两个任务，即作为主要任务的关注层和作为辅助任务的阶段层。*
-   The output of attention layer directly conveys macro strategy embedding to micro level models, while resource layer acts as an axillary task which help refine the shared layers between attention and phase tasks.

*关注层的输出直接将宏观策略嵌入传递到微观模型，而资源层则作为一个有用的任务，有助于细化关注和阶段任务之间的共享层。*
-   The illustrating network structure of HMS is listed in Figure. 2.

*HMS的说明网络结构如图所示2。*
-   HMS takes both image and vector features as input, carrying visual features and global features respectively.

*HMS将图像和矢量特征作为输入，分别承载视觉特征和全局特征。*
-   In image part, we use convolutional layers.

*在图像部分，我们使用卷积层。*
-   In vector part, we use fully connected layers.

*在矢量部分，我们使用完全连接的层。*
-   The image and vector parts merge in two separate tasks, i.e., attention and phase.

*图像和矢量部分合并在两个单独的任务中，即注意力和阶段。*
-   Ultimately, attention and phase tasks take input from shared layers through their own layers and output to compute loss.

*最终，注意力和阶段任务从共享层通过它们自己的层输出，并输出到计算损失。*

## Attention Layer<a id="sec-4-2" name="sec-4-2"></a>

-   Similar to how players make decisions according to the game map, attention layer predicts the best region for agents to move to.

*与玩家根据游戏地图做出决策的方式类似，注意力层会预测AI移动的最佳区域。*
-   However, it is tricky to tell from data that where is a player’s destination.

*但是，从数据中判断出玩家的目的地在哪里是很棘手的。*
-   We observe that regions where attack takes place can be indicator of players’ destination, because otherwise players would not have spent time on such spots.

*我们观察到发生攻击的区域可以指示玩家的目的地，因为否则玩家将不会花时间在这些位置上。*
-   According to this observation, we define ground-truth regions as the regions where players conduct their next attack.

*根据这一观察，我们将地面真实区域定义为玩家进行下一次攻击的区域。*
-   An illustrating example is shown in Figure. 3.

*一个说明性的例子如图所示3。*
-   Let s to be one session in a game which contains several frames, and s − 1 indicates the session right before s.

*设s是包含多个帧的游戏中的一个会话，s-1表示在s之前的会话。*
-   In Figure. 3, s − 1 is the first session in the game.

*在图中。 3，s  -  1是游戏中的第一个会话。*
-   Let ts to be the starting frame of s. Note that a session ends along with attack behavior, therefore there exists a region ys in ts where the hero conducts attack.

*让ts成为s的起始框架。 请注意，会话以攻击行为结束，因此在英雄进行攻击的ts中存在区域y。*
-   As shown in Figure. 3, label for s−1 is ys, while label for s is ys+1.

*如图所示。 3，s-1的标签是ys，而s的标签是ys + 1。*
-   Intuitively, by setting up labels in this way, we expect agents to learn to move to ys at the beginning of game.

*直观地说，通过以这种方式设置标签，我们希望AI学会在游戏开始时转向ys。*
-   Similarly, agents are supposed to move to appropriate regions given game situation.

*同样，考虑到游戏情况，AI应该移动到适当的区域。*

## Phase layer<a id="sec-4-3" name="sec-4-3"></a>

-   Phase layer aims to recognize the current phase.

*阶段层旨在识别当前阶段。*
-   Extracting game phases ground-truth is difficult because phase definition used by human players is abstract.

*提取游戏阶段的真实性很难，因为人类玩家使用的阶段定义是抽象的。*
-   Although roughly correlated to time, phases such as opening, laning, and late game depend on complicated judgment based on current game situation, which makes it difficult to extract groundtruth of game phases from replays.

*尽管与时间大致相关，但是开场，比赛和后期比赛等阶段取决于基于当前比赛情况的复杂判断，这使得难以从重放中提取比赛阶段的真实性。*
-   Fortunately, we observe clear correlation between game phases with major resources.

*幸运的是，我们观察到游戏阶段与主要资源之间的明确关联。*
-   For example, during the opening phase players usually aim at taking outer turrets and baron, while for late game, players operate to destroy enemies’ base.

*例如，在开始阶段，玩家通常会瞄准外部炮塔和男爵，而在游戏后期，玩家可以摧毁敌人的基地。*
-   Therefore, we propose to model phases with respect to major resources.

*因此，我们建议对主要资源进行阶段性建模。*
-   More specifically, major resources indicate turrets, baron, dragon, and base.

*更具体地说，主要资源表示炮塔，男爵，龙和基地。*
-   We marked the major resources on the map in Figure. 4a.

*我们在图中标出了地图上的主要资源4A。*
-   Label definition of phase layer is similar to attention layer.

*相位层的标签定义类似于注意层。*
-   The only difference is that ys in phase layer indicates attack behavior on turrets, baron, dragon, and base instead of in regions.

*唯一的区别是相位层中的ys表示炮塔，男爵，龙和基地的攻击行为而不是区域。*
-   Intuitively, phase layer modeling splits the entire game into several phases via modeling which macro resource to take in current phase.

*直观地说，相位层建模通过建模当前阶段的宏资源来将整个游戏分成几个阶段。*
-   We do not consider other resources such as lane creeps, heroes, and neutral creeps as major objectives because usually these resources are for bigger goal, such as destroying turrets or base with higher chance.

*我们不会将其他资源（例如车道爬行，英雄和中立生物）视为主要目标，因为通常这些资源是为了更大的目标，例如摧毁炮塔或更高机会的基地。*
-   Figure. 4b shows a series of attack behavior during the bottom outer turret strategy.

*数字。 图4b示出了在底部外转塔策略期间的一系列攻击行为。*
-   The player killed two neutral creeps in the nearby jungle and several lane creeps in the bottom lane before attacking the bottom outer turret.

*在攻击底部外部炮塔之前，该玩家在附近的丛林中杀死了两名中立小兵，并在底部小巷中杀死了几条小路。*
-   We expect the model to learn when and what major resources to take given game situation, and in the meanwhile learn attention distribution that serve each of the major resources.

*我们希望模型能够了解在游戏情况下何时以及需要采取哪些主要资源，同时学习为每个主要资源提供服务的注意力分配。*

## Imitated Cross-agents Communication<a id="sec-4-4" name="sec-4-4"></a>

-   Cross-agents communication is essential for a team of agents to cooperate.

*跨代理通信对于代理团队合作至关重要。*
-   There is rich literature of cross-agent communication on multi-agent reinforcement learning research (Sukhbaatar, Fergus, and others 2016; Foerster et al. 2016).

*关于多智能体强化学习研究的跨代理交流有丰富的文献。*
-   However, it is challenging to learn communication using training data in supervised learning because the actual communication is unknown.

*然而，在监督学习中使用训练数据来学习通信是具有挑战性的，因为实际的通信是未知的。*
-   To enable agents to communicate in supervised learning setting, we have designed a novel cross-agents communication mechanism.

*为了使代理能够在监督学习环境中进行通信，我们设计了一种新颖的跨代理通信机制。*
-   During training phase, we put attention labels of allies as features for training.

*在训练阶段，我们将盟友的注意标签作为训练的特征。*
-   During testing phase, we put attention prediction of allies as features and make decision correspondingly.

*在测试阶段，我们将盟友的注意预测作为特征进行相应的决策。*
-   In this way, our agents can "communicate" with one another and learn to cooperate upon allies’ decisions.

*通过这种方式，我们的代理人可以相互“沟通”，并学会根据盟友的决定进行合作。*
-   We name this mechanism as Imitated Crossagents Communication due to its supervised nature.

*由于其监督性质，我们将此机制命名为模仿交叉口通信。*

# Experiments<a id="sec-5" name="sec-5"></a>

-   In this section, we evaluate our model performance.

*在本节中，我们将评估模型的性能。*
-   We first describe the experimental setup, including data preparation and model setup.

*我们首先描述实验设置，包括数据准备和模型设置。*
-   Then, we present qualitative results such as attention distribution under different phase.

*然后，我们提出了不同阶段的注意力分布等定性结果。*
-   Finally, we list the statistics of matches with human player teams and evaluate improvement brought by our proposed model.

*最后，我们列出了与人类玩家团队匹配的统计数据，并评估了我们提出的模型带来的改进。*

## Experimental Setup<a id="sec-5-1" name="sec-5-1"></a>

### Data Preparation<a id="sec-5-1-1" name="sec-5-1-1"></a>

-   To train a model, we collect around 300 thousand game replays made of King Professional League competition and training records.

*为了训练模型，我们收集了大约30万个由King Professional League比赛和训练记录组成的比赛重播。*
-   Finally, 250 million instances were used for training.

*最后，2.5亿个实例用于培训。*
-   We consider both visual and attributes features.

*我们考虑视觉和属性功能。*
-   On visual side, we extract 85 features such as position and hit points of all units and then blur the visual features into 12\*12 resolution.

*在视觉方面，我们提取85个特征，例如所有单位的位置和生命点，然后将视觉特征模糊为12 \* 12分辨率。*
-   On attributes side, we extract 181 features such as roles of heroes, time period of game, hero ID, heroes’ gold and level status and Kill-DeathAssistance statistics.

*在属性方面，我们提取了181个特征，如英雄的角色，游戏的时间段，英雄ID，英雄的黄金和等级状态以及杀戮死亡协助统计数据。*

### Model Setup<a id="sec-5-1-2" name="sec-5-1-2"></a>

-   We use a mixture of convolutional and fully-connected layers to take inputs from visual and attributes features respectively.

*我们使用卷积和完全连接层的混合来分别从视觉和属性特征中获取输入。*
-   On convolutional side, we set five shared convolutional layers, each with 512 channels, padding = 1, and one RELU.

*在卷积方面，我们设置了五个共享卷积层，每个层有512个通道，padding = 1和一个RELU。*
-   Each of the tasks has two convolutional layers with exactly the same configuration with shared layers.

*每个任务都有两个卷积层，与共享层完全相同。*
-   On fully-connected layers side, we set two shared fully-connected layers with 512 nodes.

*在完全连接的层侧，我们设置了两个共享的全连接层，其中包含512个节点。*
-   Each of the tasks has two fully-connected layers with exactly the same configuration with shared layers.

*每个任务都有两个完全连接的层，具有完全相同的配置和共享层。*
-   Then, we use one concatenation layer and two fully-connected layers to fuse results of convolutional layers and fully-connected layers.

*然后，我们使用一个级联层和两个完全连接的层来融合卷积层和完全连接层的结果。*
-   We use ADAM as the optimizer with base learning rate at 10e-6.

*我们使用ADAM作为优化器，基本学习率为10e-6。*
-   Batch size was set at 128.

*批量大小设置为128。*
-   The loss weights of both phase and attention tasks are set at 1.

*阶段和注意力任务的损失权重设置为1。*
-   We used CAFFE (Jia et al. 2014) with eight GPU cards.

*我们使用CAFFE和八张GPU卡。*
-   The duration to train an HMS model was about 12 hours.

*训练HMS模型的持续时间约为12小时。*
-   Finally, the output for attention layer corresponds to 144 regions of the map, resolution of which is exactly the same as the visual inputs.

*最后，关注层的输出对应于地图的144个区域，其分辨率与视觉输入完全相同。*
-   The output of the phase task corresponds to 14 major resources circled in Figure. 4a.

*阶段任务的输出对应于图中圈出的14个主要资源4A。*

## Experimental Results<a id="sec-5-2" name="sec-5-2"></a>

### Opening Attention<a id="sec-5-2-1" name="sec-5-2-1"></a>

-   Opening is one of the most important strategies in MOBA.

*开放是MOBA最重要的策略之一。*
-   We show one opening attention of different heroes learned by our model in Figure. 5.

*我们展示了我们的模型在图中学到的不同英雄的一个开放式关注5。*
-   In Figure. 5, each subfigure consists of two square images.

*在图中。 在图5中，每个子图包括两个方形图像。*
-   The lefthand-side square image indicates the attention distribution of the right-hand-side MOBA mini-map.

*左手侧方图像表示右手侧MOBA小地图的注意力分布。*
-   The hottest region is highlighted with red circle.

*最热的区域用红色圆圈突出显示。*
-   We list attention prediction of four heroes, i.e., Diaochan, Hanxin, Arthur, and Houyi.

*我们列出了四个英雄的注意力预测，即Diaochan，Hanxin，Arthur和Houyi。*
-   The four heroes belong to master, assasin, warrior, and archer respectively.

*这四位英雄分别属于大师，刺客，战士和弓箭手。*
-   According to the attention prediction, Diaochan is dispatched to middle lane, Hanxin will move to left jungle area, and Authur and Houyi will guard the bottom jungle area.

*/根据关注预测，Diaochan将被派往中间车道，Hanxin将移至左侧丛林区域，而Authur和Houyi将守卫底部丛林区域。*
-   The fifth hero Miyamoto Musashi, which was not plotted, will guard the top outer turret.

*未编制的第五位英雄宫本武藏将守卫顶部外部炮塔。*
-   This opening is considered safe and efficient, and widely used in Honour of Kings games.

*这个开放被认为是安全和有效的，并广泛用于王者荣耀。*

### Attention Distribution Affected by Phase Layer<a id="sec-5-2-2" name="sec-5-2-2"></a>

-   We visualize attention distribution of different phases in Figure. 6a and 6b.

*我们将图中不同阶段的注意力分布可视化。 6a和6b。*
-   We can see that attention distributes around the major resource of each phase.

*我们可以看到，注意力分布在每个阶段的主要资源周围。*
-   For example, for upper outer turret phase in Figure. 6a, the attention distributes around upper outer region, as well as nearby jungle area.

*例如，对于图中的上外转塔阶段。 如图6a所示，注意力分布在上部外部区域以及附近的丛林区域。*
-   Also, as shown in Figure. 6b, attention distributes mainly in the middle lane, especially area in front of the base.

*另外，如图所示。 如图6b所示，注意力主要分布在中间车道，特别是基地前方的区域。*
-   These examples show that our phase layer modeling affects attention distribution in practice.

*这些例子表明我们的相位层建模影响了实践中的注意力分配。*
-   To further examine how phase layer correlates with game phases, we conduct t-Distributed Stochastic Neighbor Embedding (t-SNE) on phase layer output.

*为了进一步研究相位层如何与游戏阶段相关，我们在相位层输出上进行t分布随机邻域嵌入（t-SNE）。*
-   As shown in Figure. 7, samples are coloured with respect to different time stages.

*如图所示。 如图7所示，样品相对于不同的时间段着色。*
-   We can observe that samples are clearly separable with respect to time stages.

*我们可以观察到样品在时间阶段是明显可分的。*
-   For example, blue, orange and green (0-10 minuets) samples place close to one another, while red and purple samples (more than 10 minuets) form another group.

*例如，蓝色，橙色和绿色（0-10分钟）样品彼此靠近，而红色和紫色样品（超过10分钟）来自另一组。*

### Macro Strategy Embedding<a id="sec-5-2-3" name="sec-5-2-3"></a>

-   We evaluate how important is the macro strategy modeling.

*我们评估宏观策略建模的重要性。*
-We removed the macro strategy embedding and trained the model using micro level actions from the replays.
*我们删除了宏观策略嵌入，并使用重放中的微观水平操作训练模型。*
-   The micro level model design is similar to OpenAI Five (OpenAI 2018a).

*微观水平模型设计类似于OpenAI Five。*
-   Detail description of the micro level modeling is out of the scope of this paper.

*微观水平建模的详细描述超出了本文的范围。*
-   The result is listed in Table. 2, column AI Without Macro Strategy.

*结果列于表中。 2，列AI没有宏观策略。*
-   As the result shows, HMS outperformed AI Without Macro Strategy with 75% winning rates.

*结果显示，HMS的表现优于人工智能无宏观战略，获胜率为75％。*
-   HMS performed much better than AI Without Macro Strategy in terms of number of kills, turrets destruction, and gold.

*在杀伤数量，炮塔破坏和黄金方面，HMS比AI无宏战略表现得更好。*
-   The most obvious performance change is that AI Without Macro Strategy mainly focused on nearby targets.

*最明显的性能变化是AI Without Macro Strategy主要关注附近的目标。*
-   Agents did not care much about backing up teammates and pushing lane creeps in relatively large distance.

*AI并不关心在相对较远的距离内备份队友和推动车道爬行。*
-   They spent most of the time on killing neutral creeps and nearby lane creeps.

*他们大部分时间都在杀死中立的小兵和附近的小路。*
-   The performance change can be observed from the comparison of engagement rate and number of turrets in Table. 2.

*表格中的啮合率和转塔数量的比较可以观察到性能的变化2。*
-   This phenomenon may reflect how important macro strategy modeling is to highlight important spots.

*这种现象可能反映了宏观战略建模对突出重要点的重要性。*

### Match against Human Players<a id="sec-5-2-4" name="sec-5-2-4"></a>

-   To evaluate our AI performance more accurately, we conduct matches between our AI and human players.

*为了更准确地评估我们的AI表现，我们在AI和人类玩家之间进行匹配。*
-   We invited 250 human player teams whose average ranking is King in Honour of Kings rank system (above 1% of human players).

*我们邀请了250名人力队员，他们的平均排名是王者级别在王者荣耀排名系统中。*
-   Following the standard procedure of ranked match in Honour of Kings, we obey ban-pick rules to pick and ban heroes before each match.

*遵循王者荣耀排名赛的标准程序，我们遵守禁赛规则，在每场比赛前挑选和禁赛英雄。*
-   The ban-pick module was implemented using simple rules.

*禁令选择模块是使用简单的规则实现的。*
-   Note that gamecores of Honour of Kings limit commands frequency to a level similar with human.

*请注意，王者荣耀的游戏币将命令频率限制在与人类相似的水平。*
-   The overall statistics are listed in Table. 2, column Human Teams.

*表中列出了总体统计数据2，专栏人力队。*
-   Our AI achieved 48% winning rate in the 250 games.

*我们的AI在250场比赛中获得了48％的胜率。*
-   The statistics show that our AI team did not have advantage on teamfight over human teams.

*统计数据显示，我们的AI团队在团队战斗中没有优势超过人类团队。*
-   The number of kills made by AI is about 15% less than human teams.

*人工智能所造成的杀戮次数比人类团队少约15％。*
-   Other items such as turrets destruction, engagement rate, and gold per minute were similar between AI and human.

*AI和人类之间的其他项目如炮塔破坏，参与率和每分钟金币相似。*
-   We have further observed that our AI destroyed 2.5 more turrets than human on average in the first 10 minutes.

*我们进一步观察到，我们的AI在前10分钟内平均摧毁了2.5个以上的炮塔。*
-   After 10 minutes, turrets difference shrank due to weaker teamfight ability compared to human teams.

*10分钟后，与人类队伍相比，由于较弱的团战能力，炮塔差异缩小。*
-   Arguably, our AI’s macro strategy operation ability is close to or above our human opponents.

*可以说，我们的人工智能的宏观战略运作能力接近或高于我们的人类对手。*

### Imitated Cross-agents Communication<a id="sec-5-2-5" name="sec-5-2-5"></a>

-   To evaluate how important the cross-agents communication mechanism is to the AI ability, we conduct matches between HMS and HMS trained without cross-agents communication.

*为了评估跨代理通信机制对AI能力的重要性，我们在没有跨代理通信的情况下进行HMS和HMS之间的匹配。*
-   The result is listed in Table. 2, column AIWithout Communication.

*结果列于表中2，列AI没有通讯。*
-   HMS achieved a 62.5% winning rate over the version without communication.

*HMS在没有通信的情况下获得了62.5％的中奖率。*
-   We have observed obvious cross-agents cooperation learned when cross-agents communication was introduced.

*我们观察到在引入跨代理通信时学到的明显的跨代理合作。*
-   For example, rate of reasonable opening increased from 22% to 83% according to experts’ evaluation.

*例如，根据专家的评估，合理开放率从22％增加到83％。*

### Phase layer<a id="sec-5-2-6" name="sec-5-2-6"></a>

-   We evaluate how phase layer affects the performance of HMS.

*我们评估相位层如何影响HMS的性能。*
-   We removed the phase layer and compared it with the full version of HMS.

*我们删除了相位层并将其与完整版HMS进行了比较。*
-   The result is listed in Table. 2, column AI Without phase layer.

*结果列于表中。 2，列AI没有相位层。*
-   The result shows that phase layer modeling improved HMS significantly with 65% winning rate.

*结果表明，相位层建模显着改善了HMS，获胜率为65％。*
-   We have also observed obvious AI ability downgrade when phase layer was removed.

*我们还观察到当移除相层时明显的AI能力降级。*
-   For example, agents were no longer accurate about timing when baron first appears, while the full version HMS agents got ready at 2:00 to gain baron as soon as possible.

*例如，当男爵首次出现时，AI不再准确定时，而完整版HMSAI在2:00准备好以尽快获得男爵。*

# Conclusion and Future Work<a id="sec-6" name="sec-6"></a>

-   In this paper, we proposed a novel Hierarchical Macro Strategy model which models macro strategy operation for MOBA games.

*在此论文中，我们提议了一个新颖的分层宏观策略模型，对MOBA游戏的宏观策略操作进行建模*
-   HMS explicitly models agents’ attention on game maps and considers game phase modeling.

*HMS清晰地代理人在游戏地图的注意力进行建模以及考虑游戏分阶段模型*
-   We also proposed a novel imitated cross-agent communication mechanism which enables agents to cooperate.

*我们也提议了一个新颖的模仿反向代理交流机制使得代理能够合作*
-   We used Honour of Kings as an example of MOBA games to implement and evaluate HMS.

*我们使用王者荣耀作为MOBA类游戏的一个例子去实践和评估HMS*
-   We conducted matches between our AI and top 1% human player teams.

*我们在AI与人类1%顶尖队伍之间安排了比赛*
-   Our AI achieves a 48% winning rate.

*我们的AI获得了48%胜率*
-   To the best of our knowledge, our proposed HMS model is the first learning based model that explicitly models macro strategy for MOBA games.

*据我们所知，我们提议HMS模型是最好的基于学习的模型，这模型清楚地建模了宏观策略用于MOBA类游戏中*
-   HMS used supervised learning to learn macro strategy operation and corresponding micro level execution from high quality replays.

*HMS使用监督学习从高质量重放中去训练宏观策略操作和相应的微观层面执行*
-   A trained HMS model can be further used as an initial policy for reinforcement learning framework.

*训练过的HMS模型能够更好的使用作为最初的策略对于增强学习框架*
-   Our proposed HMS model exhibits a strong potential in MOBA games.

*我们提出了HMS模型显示了非常强大的潜力在MOBA游戏中*
-   It may be generalized to more RTS games with appropriate adaptations.

*这可能总结了RTS游戏，他对RTS游戏有着较好的适应性*
-   For example, the attention layer modeling may be applicable to StarCraft, where the definition of attention can be extended to more meaningful behaviors such as building operation.

*例如，注意力层建模能够适应与星际争霸，注意力的限定能够使得更多有意义的行为例如建立操作*
-   Also, Imitated Crossagents Communication can be used to learn to cooperate.

*除此之外，模仿反向代理通讯习惯于学习配合*
-   Phase layer modeling is more game-specific.

*分阶段层建模更加具体于游戏*
-   The resource collection procedure in StarCraft is different from that of MOBA, where gold is mined near the base.

*星际争霸的资源整合程序不同于其他的moba类游戏，他的黄金是离基地十分之近*
-   Therefore, phase layer modeling may require game-specific design for different games.

*因此，对于不同的游戏，分阶段层建模可能需要具体游戏具体设计*
-   However, the underlying idea to capture game phases can be generalized to Starcraft as well.

*然而，捕获游戏阶段的这个根本思想也能够广泛地用于星际争霸*
-   HMS may also inspire macro strategy modeling in domains where multiple agents cooperate on a map and historical data is available.

*HMS可能也鼓舞了宏观策略建模领域，当在地图上多人代理合作且历史数据可使用*
-   For example, in robot soccer, attention layer modeling and Imitated Cross-agents Communication may help robots position and cooperate given parsed soccer recordings.

*例如，在机器人足球中，注意力层建模和模仿反向代理通讯可能帮助机器人状况和合作去提供解析的足球记录*
-   In the future, we will incorporate planning based on HMS.

*在未来，我们将在HMS的基础上纳入计划*
-   Planning by MCTS roll-outs in Go has been proven essential to outperform top human players (Silver et al. 2016).

*通过在GO中的MCTS实现规划被证实对于击败顶尖人类玩家起到至关重要的一幕*
-   We expect planning can be essential for RTS games as well, because it may not only be useful for imperfect information gaming but also be crucial to bringing in expected rewards which supervised learning fails to consider.

*我们期望此规划对于RTS游戏也能够起到至关重要,因为它不仅对于不完美信息游戏中有用，而且带来期望在监督学习欠考虑到的奖赏是至关重要的*

# References<a id="sec-7" name="sec-7"></a>

[DeLoura 2001] DeLoura, M. A. 2001. Game programming gems 2. Cengage learning.

[do Nascimento Silva and Chaimowicz 2015] do Nascimento Silva, V., and Chaimowicz, L. 2015. On the development of intelligent agents for moba games. In Computer Games and Digital Entertainment (SBGames), 2015 14th Brazilian Symposium on, 142–151. IEEE. [DOTA2 2018] DOTA2. 2018. The international 2018. <https://www.dota2.com/international/announcement/>.

[Foerster et al. 2016] Foerster, J. N.; Assael, Y. M.; de Freitas, N.; and Whiteson, S. 2016. Learning to communicate to solve riddles with deep distributed recurrent q-networks. arXiv preprint arXiv:1602.02672.

[Hagelbäck and Johansson 2008] Hagelbäck, J., and Johansson, S. J. 2008. The rise of potential fields in real time strategy bots. In Fourth Artificial Intelligence and Interactive Digital Entertainment Conference. Stanford University.

[Jia et al. 2014] Jia, Y.; Shelhamer, E.; Donahue, J.; Karayev, S.; Long, J.; Girshick, R.; Guadarrama, S.; and Darrell, T. 2014. Caffe: Convolutional architecture for fast feature embedding. arXiv preprint arXiv:1408.5093. [Murphy 2015] Murphy, M. 2015. Most played games: November 2015 – fallout 4 and black ops iii arise while starcraft ii shines. <http://caas.raptr.com/most-played-gamesnovember-2015-fallout-4-andblack-ops-iii-arise-whilestarcraft-ii-shines/>.

[Ontanón and Buro 2015] Ontanón, S., and Buro, M. 2015. Adversarial hierarchical-task network planning for complex real-time games. In Twenty-Fourth International Joint Conference on Artificial Intelligence.

[OpenAI 2018a] OpenAI. 2018a. Openai blog: Dota 2. <https://blog.openai.com/dota-2/> (17 Apr 2018). [OpenAI 2018b] OpenAI.
2018b. Openai five. <https://blog.openai.com/openai-five/> (25 Jun 2018).

[Schulman et al. 2017] Schulman, J.; Wolski, F.; Dhariwal, P.; Radford, A.; and Klimov, O. 2017. Proximal policy optimization algorithms. arXiv preprint arXiv:1707.06347.

[Silva and Chaimowicz 2017] Silva, V. D. N., and Chaimowicz, L. 2017. Moba: a new arena for game ai. arXiv preprint arXiv:1705.10443.

[Silver et al. 2016] Silver, D.; Huang, A.; Maddison, C. J.; Guez, A.; Sifre, L.; Van Den Driessche, G.; Schrittwieser, J.; Antonoglou, I.; Panneershelvam, V.; Lanctot, M.; et al. 2016. Mastering the game of go with deep neural networks and tree search. nature 529(7587):484–489.

[Simonite 2018] Simonite, T. 2018. Pro gamers fend off elon musk-backed ai bots—for now.
<https://www.wired.com/story/pro-gamers-fend-off-elonmusks-ai-bots/> (Aug 23, 2018). [Sukhbaatar, Fergus, and others 2016] Sukhbaatar, S.; Fergus, R.; et al. 2016. Learning multiagent communication with backpropagation. In Advances in Neural Information Processing Systems, 2244–2252. [SuperData 2018] SuperData.
wide digital games market: February 2018. <https://www.superdataresearch.com/us-digital-gamesmarket/>.

[Synnaeve and Bessiere 2011] Synnaeve, G., and Bessiere, P. 2011. A bayesian model for rts units control applied to starcraft. In Computational Intelligence and Games (CIG), 2011 IEEE Conference on, 190–196. IEEE.

[Tian et al. 2017] Tian, Y.; Gong, Q.; Shang, W.; Wu, Y.; and Zitnick, C. L. 2017. Elf: An extensive, lightweight and flexible research platform for real-time strategy games. In Advances in Neural Information Processing Systems, 2656– 2666.

[Vincent 2018] Vincent, J. 2018. Humans grab victory in first of three dota 2 matches against openai. <https://www.theverge.com/2018/8/23/17772376/openaidota-2-pain-game-human-victory-ai> (Aug 23, 2018).

[Vinyals et al. 2017] Vinyals, O.; Ewalds, T.; Bartunov, S.; Georgiev, P.; Vezhnevets, A. S.; Yeo, M.; Makhzani, A.; Küttler, H.; Agapiou, J.; Schrittwieser, J.; et al. 2017. Starcraft ii: a new challenge for reinforcement learning. arXiv preprint arXiv:1708.04782.

[Wender and Watson 2012] Wender, S., and Watson, I. 2012. Applying reinforcement learning to small scale combat in the real-time strategy game starcraft: Broodwar. In Computational Intelligence and Games (CIG), 2012 IEEE Conference on, 402–408. IEEE.