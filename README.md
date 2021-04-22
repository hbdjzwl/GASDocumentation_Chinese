**更新时间：2021/03/26(完结)**  
翻译此文时间: 2020.12.21  
原文地址: [tranek/GASDocumentation](https://github.com/BillEliot/GASDocumentation)  
翻译地址: [BillEliot/GASDocumentation_Chinese](https://github.com/BillEliot/GASDocumentation_Chinese)  
反馈: **github/PR** or **eliotwjz@gmail.com** or **wujia.ze@qq.com**

# GASDocumentation

我使用一个简单的多人游戏模板项目来阐述对Unreal Engine 4中GameplayAbilitySystem(GAS)插件的理解. 这不是官方文档并且这个项目和我自己都不来自Epic Games. 我不能保证该文档的准确性.  

该文档的目的是阐明GAS中的主要概念和相关类, 并结合我的经验提供一些附加说明. 在社区用户中, 已经形成了大量有关GAS的"部落知识", 而我致力于将我了解的全部在这里分享.  

样例项目和文档目前基于`Unreal Engine 4.26`. 该文档拥有可用于旧版本Unreal Engine的分支, 但是它们不再受支持, 并且可能存在bug和过时信息.  
[GASShooter](https://github.com/tranek/GASShooter)是该样例项目的姐妹项目, 其演示了基于多人FPS/TPS的高阶GAS技术.  

最好的文档永远是该插件的代码.  

---

<a name="table-of-contents"></a>
## 目录

* [1. 步入GameplayAbilitySystem插件](#intro)
* [2. 样例项目](#sp)
* [3. 使用GAS创建一个项目](#setup)
* [4. GAS概念](#concepts)
    + [4.1 Ability System Component](#concepts-asc)
        * [4.1.1 同步模式](#concepts-asc-rm)
        * [4.1.2 设置和初始化](#concepts-asc-setup)
    + [4.2 Gameplay Tags](#concepts-gt)
        * [4.2.1 响应Gameplay Tags的变化](#concepts-gt-change)
    + [4.3 Attribute](#concepts-a)
        * [4.3.1 Attribute定义](#concepts-a-definition)
        * [4.3.2 BaseValue vs. CurrentValue](#concepts-a-value)
        * [4.3.3 元(Meta)Attribute](#concepts-a-meta)
        * [4.3.4 响应Attribute变化](#concepts-a-changes)
        * [4.3.5 自动推导Attribute](#concepts-a-derived)
    + [4.4 AttributeSet](#concepts-as)
        * [4.4.1 定义AttributeSet](#concepts-as-definition)
        * [4.4.2 设计AttributeSet](#concepts-as-design)
            + [4.4.2.1 使用单独Attribute的子组件](#concepts-as-design-subcomponents)
            + [4.4.2.2 运行时添加和移除AttributeSet](#concepts-as-design-addremoveruntime)
            + [4.4.2.3 Item Attribute(武器弹药)](#concepts-as-design-itemattributes)
                * [4.4.2.3.1 在物品中使用普通浮点数](#concepts-as-design-itemattributes-plainfloats)
                * [4.4.2.3.2 在物品中使用AttributeSet](#concepts-as-design-itemattributes-attributeset)
                * [4.4.2.3.3 在物品中使用单独的ASC](#concepts-as-design-itemattributes-asc)
        * [4.4.3 定义Attribute](#concepts-as-attributes)
        * [4.4.4 初始化Attribute](#concepts-as-init)
        * [4.4.5 PreAttributeChange()](#concepts-as-preattributechange)
        * [4.4.6 PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)
        * [4.4.7 OnAttributeAggregatorCreated()](#concepts-as-onattributeaggregatorcreated)
    + [4.5 Gameplay Effects](#concepts-ge)
        * [4.5.1 定义GameplayEffect](#concepts-ge-definition)
        * [4.5.2 应用GameplayEffect](#concepts-ge-applying)
        * [4.5.3 移除GameplayEffect](#concepts-ga-removing)
        * [4.5.4 GameplayEffectModifier](#concepts-ge-mods)
            + [4.5.4.1 Multiply和Divide Modifier](#concepts-ge-mods-multiplydivide)
            + [4.5.4.2 Modifier的GameplayTag](#concepts-ge-mods-gameplaytags)
        * [4.5.5 GameplayEffect堆栈](#concepts-ge-stacking)
        * [4.5.6 授予Ability](#concepts-ge-ga)
        * [4.5.7 GameplayEffect标签](#concepts-ge-tags)
        * [4.5.8 免疫](#concepts-ge-immunity)
        * [4.5.9 GameplayEffectSpec](#concepts-ge-spec)
            + [4.5.9.1 SetByCaller](#concepts-ge-spec-setbycaller)
        * [4.5.10 GameplayEffectContext](#concepts-ge-context)
        * [4.5.11 Modifier Magnitude Calculation](#concepts-ge-mmc)
        * [4.5.12 Gameplay Effect Execution Calculation](#concepts-ge-ec)
            + [4.5.12.1 发送数据到Execution Calculation](#concepts-ge-ec-senddata)
                * [4.5.12.1.1 SetByCaller](#concepts-ge-ec-senddata-setbycaller)
                * [4.5.12.1.2 支持(Backing)数据Attribute计算Modifier](#concepts-ge-ec-senddata-backingdataattribute)
                * [4.5.12.1.3 支持(Backing)数据临时变量计算Modifier](#concepts-ge-ec-senddata-backingdatatempvariable)
                * [4.5.12.1.4 GameplayEffectContext](#concepts-ge-ec-senddata-effectcontext)
        * [4.5.13 自定义应用需求](#concepts-ge-car)
        * [4.5.14 花费(Cost)GameplayEffect](#concepts-ge-cost)
        * [4.5.15 冷却(Cooldown)GameplayEffect](#concepts-ge-cooldown)
            + [4.5.15.1 获取Cooldown GameplayEffect的剩余时间](#concepts-ge-cooldown-tr)
            + [4.5.15.2 监听冷却开始和结束](#concepts-ge-cooldown-listen)
            + [4.5.15.3 预测冷却时间](#concepts-ge-cooldown-prediction)
        * [4.5.16 修改已激活GameplayEffect的持续时间](#concepts-ge-duration)
        * [4.5.17 在运行时创建动态GameplayEffect](#concepts-ge-dynamic)
        * [4.5.18 GameplayEffect Containers](#concepts-ge-containers)
    + [4.6 Gameplay Abilities](#concepts-ga)
        * [4.6.1 GameplayAbility定义](#concepts-ga-definition)
            + [4.6.1.1 Replication Policy](#concepts-ga-definition-reppolicy)
            + [4.6.1.2 Server Respects Remote Ability Cancellation](#concepts-ga-definition-remotecancel)
            + [4.6.1.3 Replicate Input Directly](#concepts-ga-definition-repinputdirectly)
        * [4.6.2 绑定输入到ASC](#concepts-ga-input)
            + [4.6.2.1 绑定输入时不激活Ability](#concepts-ga-input-noactivate)
        * [4.6.3 授予Ability](#concepts-ga-granting)
        * [4.6.4 激活Ability](#concepts-ga-activating)
            + [4.6.4.1 被动Ability](#concepts-ga-activating-passive)
        * [4.6.5 取消Ability](#concepts-ga-cancelabilities)
        * [4.6.6 获取激活的Ability](#concepts-ga-definition-activeability)
        * [4.6.7 实例化策略](#concepts-ga-instancing)
        * [4.6.8 网络执行策略(Net Execution Policy)](#concepts-ga-net)
        * [4.6.9 Ability标签](#concepts-ga-tags)
        * [4.6.10 Gameplay Ability Spec](#concepts-ga-spec)
        * [4.6.11 传递数据到Ability](#concepts-ga-data)
        * [4.6.12 Ability花费(Cost)和冷却(Cooldown)](#concepts-ga-commit)
        * [4.6.13 升级Ability](#concepts-ga-leveling)
        * [4.6.14 Ability集合](#concepts-ga-sets)
        * [4.6.15 Ability批处理](#concepts-ga-batching)
        * [4.6.16 网络安全策略(Net Security Policy)](#concepts-ga-netsecuritypolicy)
    + [4.7 Ability Tasks](#concepts-at)
        * [4.7.1 AbilityTask定义](#concepts-at-definition)
        * [4.7.2 自定义AbilityTask](#concepts-at-definition)
        * [4.7.3 使用AbilityTask](#concepts-at-using)
        * [4.7.4 Root Motion Source Ability Task](#concepts-at-rms)
    + [4.8 Gameplay Cues](#concepts-gc)
        * [4.8.1 GameplayCue定义](#concepts-gc-definition)
        * [4.8.2 触发GameplayCue](#concepts-gc-trigger)
        * [4.8.3 客户端GameplayCue](#concepts-gc-local)
        * [4.8.4 GameplayCue参数](#concepts-gc-parameters)
        * [4.8.5 Gameplay Cue Manager](#concepts-gc-manager)
        * [4.8.6 阻止GameplayCue响应](#concepts-gc-prevention)
        * [4.8.7 GameplayCue批处理](#concepts-gc-batching)
            + [4.8.7.1 手动RPC](#concepts-gc-batching-manualrpc)
            + [4.8.7.2 GameplayEffect中的多个GameplayCue](#concepts-gc-batching-gcsonge)
    + [4.9 Ability System Globals](#concepts-asg)
        * [4.9.1 InitGlobalData()](#concepts-asg-initglobaldata)
    + [4.10 预测(Prediction)](#concepts-p)
        * [4.10.1 Prediction Key](#concepts-p-key)
        * [4.10.2 在Ability中创建新的预测窗口(Prediction Window)](#concepts-p-windows)
        * [4.10.3 预测性地生成Actor](#concepts-p-spawn)
        * [4.10.4 GAS中预测的未来](#concepts-p-future)
        * [4.10.5 网络预测插件(Network Prediction plugin)](#concepts-p-npp)
    + [4.11 Targeting](#concepts-targeting)
        * [4.11.1 Target Data](#concepts-targeting-data)
        * [4.11.2 Target Actor](#concepts-targeting-actors)
        * [4.11.3 TargetData过滤器](#concepts-target-data-filters)
        * [4.11.4 Gameplay Ability World Reticles](#concepts-targeting-reticles)
        * [4.11.5 Gameplay Effect Containers Targeting](#concepts-targeting-containers)
* [5. 常用的Abilty和Effect](#cae)
    + [5.1 眩晕(Stun)](#cae-stun)
    + [5.2 奔跑(Sprint)](#cae-sprint)
    + [5.3 瞄准(Aim Down Sight)](#cae-ads)
    + [5.4 生命偷取(Lifesteal)](#cae-ls)
    + [5.5 在客户端和服务端中生成随机数](#cae-random)
    + [5.6 暴击(Critical Hits)](#cae-crit)
    + [5.7 非堆栈GameplayEffect, 但是只有其最高级(Greatest Magnitude)才能实际影响Target](#cae-nonstackingge)
    + [5.8 游戏暂停时生成TargetData](#cae-paused)
    + [5.9 按钮交互系统(Button Interaction System)](#cae-onebuttoninteractionsystem)
* [6. 调试GAS](#debugging)
    + [6.1 showdebug abilitysystem](#debugging-sd)
    + [6.2 Gameplay Debugger](#debugging-gd)
    + [6.3 GAS日志(Logging)](#debugging-log)
* [7. 优化](#optimizations)
    + [7.1 Ability批处理](#optimizations-abilitybatching)
    + [7.2 GameplayCue批处理](#optimizations-gameplaycuebatching)
    + [7.3 AbilitySystemComponent同步模式(Replication Mode)](#optimizations-ascreplicationmode)
    + [7.4 Attribute代理同步](#optimizations-attributeproxyreplication)
    + [7.5 ASC懒加载](#optimizations-asclazyloading)
* [8. Quality of Life Suggestions](#qol)
    + [8.1 Gameplay Effect Containers](#qol-gameplayeffectcontainers)
    + [8.2 将蓝图AsyncTask绑定到ASC委托](#qol-asynctasksascdelegates)
* [9. 疑难解答](#troubleshooting)
    + [9.1 LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!](#troubleshooting-notlocal)
    + [9.2 ScriptStructCache错误](#troubleshooting-scriptstructcache)
    + [9.3 动画蒙太奇不能同步到客户端](#troubleshooting-replicatinganimmontages)
* [10. ASC常用术语缩略](#acronyms)
* [11. 其他资源](#resources)
* [12. GAS更新日志](#changelog)
	+ [12.1 4.26](#changelog-4.26)
	+ [12.2 4.25.1](#changelog-4.25.1)
	+ [12.3 4.25](#changelog-4.25)
	+ [12.4 4.24](#changelog-4.24)

---

<a name="intro"></a>
## 1. 步入GameplayAbilitySystem插件

摘自[官方文档](https://docs.unrealengine.com/en-US/Gameplay/GameplayAbilitySystem/index.html):  
> Gameplay技能系统 是一个高度灵活的框架，可用于构建你可能会在RPG或MOBA游戏中看到的技能和属性类型。你可以构建可供游戏中的角色使用的动作或被动技能，使这些动作导致各种属性累积或损耗的状态效果，实现约束这些动作使用的"冷却"计时器或资源消耗，更改技能等级及每个技能等级的技能效果，激活粒子或音效，等等。简单来说，此系统可帮助你在任何现代RPG或MOBA游戏中设计、实现及高效关联各种游戏中的技能，既包括跳跃等简单技能，也包括你喜欢的角色的复杂技能集。
 
GameplayAbilitySystem插件由Epic Games开发, 随Unreal Engine 4 (UE4)发布. 它已经由3A商业游戏的严格测试, 例如帕拉贡(Paragon)和堡垒之夜(Fortnite)等等.  

该插件对于单人和多人游戏提供了开箱即用的解决方案:  

* 执行基于等级的角色能力(Ability)或技能(Skill), 该能力或技能可选花费和冷却时间. ([GameplayAbility](#concepts-ga))
* 管理属于Actor的数值Attribute. ([Attribute](#concepts-a))
* 为Actor应用状态效果. ([GameplayEffect](#concepts-ge))
* 为Actor应用GameplayTag. ([GameplayTag](#concepts-gt))
* 生成视觉或声音效果. ([GameplayCue](#concepts-gc))
* 为以上提到的所有应用同步(Replication).

在多人游戏中, GAS提供客户端预测([client-side prediction](#concepts-p))支持:  

* 能力激活.
* 播放蒙太奇.
* 对`Attribute`的修改.
* 应用`GameplayTag`.
* 生成`GameplayCue`.
* 通过连接于`CharacterMovementComponent`的`RootMotionSource`函数形成的移动.

**GAS必须由C++创建**, 但是`GameplayAbility`和`GameplayEffect`可由设计师在蓝图中创建.  

GAS中的现存问题:

* `GameplayEffect`延迟调节(Latency Reconciliation).(不能预测能力冷却时间, 导致高延迟玩家相比低延迟玩家, 对于短冷却时间的能力有更低的激活速率.)
* 不能预测性地移除`GameplayEffect`. 然而我们可以反向预测性地添加`GameplayEffect`, 从而高效的移除它. 但是这不总是合适或者可行的, 因此这仍然是个问题.
* 缺乏样例模板项目, 多人联机样例和文档. 希望这篇文档会有所帮助.

**[⬆ 返回目录](#table-of-contents)**

<a name="sp"></a>
## 2. 样例项目

该文档包含一个支持多人联机的第三人称射击游戏模板项目, 其目标受众为初识`GameplayAbilitySystem`插件, 但并不是Unreal Engine 4新手. 用户应该了解C++, 蓝图, UMG, Replication和其他UE4的中间件. 该项目提供了一个样例, 其向你展示了如何使用`GameplayAbilitySystem`插件建立一个基础的支持多人联机的第三人称射击游戏, 其中`AbilitySystemComponent(ASC)`分别位于`PlayerState`类代表玩家/AI控制的人物和位于`Character`类代表AI控制的小兵.  
我在保证展现GAS基础和带有完整注释的代码所表示的一些普遍技能的同时, 尽力使这个样例足够简单. 由于该文档专注于初学者, 因此该样例不包含像[Predicting Projectiles](#concepts-p-spawn)这样的高阶技术.  
概念说明:  
* `ASC`位于`PlayerState`还是`Character`.
* 网络同步的`Attribute`.
* 网络同步的蒙太奇(Animation Montages).
* `GameplayTag`.
* 在`GameplayAbility`内部和外部应用和移除`GameplayEffect`.
* 应用被护甲防御后的伤害值来修改角色生命值.
* `GameplayEffectExecutionCalculations`.
* 眩晕效果.
* 死亡和重生.
* 在服务端上使用能力(Ability)生成抛射物(Projectile).
* 在瞄准和奔跑时, 预测性的修改本地玩家速度.
* 不断消耗耐力来奔跑.
* 消耗魔法值来使用能力(Ability).
* 被动能力(Ability).
* 堆栈`GameplayEffect`.
* 锁定Actor.
* 在蓝图中创建`GameplayAbility`.
* 在C++中创建`GameplayAbility`.
* 实例化每个Actor的`GameplayAbility`.
* 非实例化的`GameplayAbility`(Jump).
* 静态`GameplayCue`(子弹撞击粒子效果).
* Actor `GameplayCue`(奔跑和眩晕粒子效果).

角色类有如下能力:  
|能力|输入绑定|是否可预测|C++/Blueprint|描述|
|:-:|:-:|:-:|:-:|:-:|
|跳跃|空格键|Yes|C++|使角色跳跃.|
|枪|鼠标左键|No|C++|从角色的枪中发射投掷物, 发射动画是可预测的, 但是投掷物不能预测.|
|瞄准|鼠标右键|Yes|Blueprint|当按住鼠标右键时, 角色会走的更慢并且摄像机会拉近(zoom in)以获得更高的射击精度.|
|奔跑|左Shift|Yes|Blueprint|当按住左Shift时, 角色在消耗体力的同时跑得更快.|
|向前猛冲|Q|Yes|Blueprint|角色消耗体力的同时向前猛冲.|
|被动护盾叠加|被动|No|Blueprint|每过4s角色获得一个最大层数为4的护盾, 每次受到伤害时移除一层护盾.|
|陨石坠落|R|No|Blueprint|角色锁定一个敌人召唤一个陨石, 对其造成伤害和眩晕效果. 定位是可以预测的而陨石生成是不可预测的.|

`GameplayAbility`无论是由蓝图还是C++创建都没关系. 这里我们使用蓝图和C++混合创建, 意在展示每种方式的使用方法.  
AI控制的小兵没有预先定义的`GameplayAbility`. 红方小兵有较多的生命回复, 蓝方小兵有较多的初始生命.  
对于`GameplayAbility`的命名, 我使用`_BP`后缀表示由蓝图创建的`GameplayAbility`逻辑, 没有后缀则表示由C++创建.  

**蓝图资源命名前缀**  
|Prefix|Asset Type|
|:-:|:-:|
|GA_|GameplayAbility|
|GC_|GameplayCue|
|GE_|GameplayEffect|

**[⬆ 返回目录](#table-of-contents)**

<a name="setup"></a>
## 3. 使用GAS创建一个项目

使用GAS建立一个项目的基本步骤:  
1. 在编辑器中启用GameplayAbilitySystem插件.
2. 编辑`YourProjectName.Build.cs`, 添加`"GameplayAbilities"`, `"GameplayTags"`, `"GameplayTasks"`到你的`PrivateDependencyModuleNames`.
3. 刷新/重新生成Visual Studio项目文件.
4. 从4.24开始, 需要强制调用`UAbilitySystemGlobals::InitGlobalData()`来使用[`TargetData`](#concepts-targeting-data), 样例项目在`UEngineSubsystem::Initialize()`中调用该函数. 参阅[`InitGlobalData()`](#concepts-asg-initglobaldata)获取更多信息.

这就是你启用GAS所需做的全部了. 从这里开始, 添加一个[`ASC`](#concepts-asc)和[`AttributeSet`](#concepts-as)到你的`Character`或`PlayerState`, 并开始着手[`GameplayAbility`](#concepts-ga)和[`GameplayEffect`](#concepts-ge)!

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts"></a>
## 4. GAS概念

* 4.1 [Ability System Component](#concepts-asc)  
* 4.2 [Gameplay Tags](#concepts-gt)  
* 4.3 [Attributes](#concepts-a)  
* 4.4 [Attribute Set](#concepts-as)  
* 4.5 [Gameplay Effects](#concepts-ge)  
* 4.6 [Gameplay Abilities](#concepts-ga)  
* 4.7 [Ability Tasks](#concepts-at)  
* 4.8 [Gameplay Cues](#concepts-gc)  
* 4.9 [Ability System Globals](#concepts-asg)  
* 4.10 [Prediction](#concepts-p)

<a name="concepts-asc"></a>
### 4.1 Ability System Component

`AbilitySystemComponent(ASC)`是GAS的核心, 它是一个处理所有与该系统交互的`UActorComponent`([UAbilitySystemComponent](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemComponent/index.html)), 所有期望使用[GameplayAbility](#concepts-ga), 包含[Attribute](#concepts-a), 或者接受[GameplayEffect](#concepts-ge)的Actor都必须附加`ASC`. 这些对象都存于`ASC`并由其管理和同步(除了由[AttributeSet](#concepts-as)同步的`Attribute`). 开发者最好但不强求继承该组件.  

`ASC`附加的`Actor`被引用作为该`ASC`的`OwnerActor`, 该`ASC`的物理代表`Actor`被称为`AvatarActor`. `OwnerActor`和`AvatarActor`可以是同一个 `Actor`, 比如MOBA游戏中的一个简单AI小兵; 它们也可以是不同的`Actor`, 比如MOBA游戏中玩家控制的英雄, 其中`OwnerActor`是`PlayerState`, `AvatarActor`是英雄的`Character`类. 绝大多数Actor的`ASC`都附加在其自身, 如果你的Actor会重生并且重生时需要持久化`Attribute`或`GameplayEffect`(比如MOBA中的英雄), 那么`ASC`理想的位置就是`PlayerState`.  

**Note:** 如果`ASC`位于PlayerState, 那么你需要提高PlayerState的`NetUpdateFrequency`, 其默认是一个很低的值, 因此在客户端上发生`Attribute`和`GameplayTag`改变时会造成延迟或卡顿. 确保启用[Adaptive Network Update Frequency](https://docs.unrealengine.com/en-US/Gameplay/Networking/Actors/Properties/index.html#adaptivenetworkupdatefrequency), Fortnite就启用了该项.  

OwnerActor需要继承并实现`IAbilitySystemInterface`, 如果AvatarActor和OwnerActor是不同的Actor, 那么AvatarActor也应该继承并实现`IAbilitySystemInterface`. 该接口有一个必须重写的函数, `UAbilitySystemComponent* GetAbilitySystemComponent() const`, 其返回一个指向`ASC`的指针, `ASC`通过寻找该接口函数来和系统内部进行交互.  

`ASC`在`FActiveGameplayEffectContainer ActiveGameplayEffect`中保存其当前活跃的`GameplayEffect`.  

`ASC`在`FGameplayAbilitySpecContainer ActivatableAbility`中保存其授予的`GameplayAbility`. 当你想遍历`ActivatableAbility.Items`时, 确保在循环体之上添加`ABILITYLIST_SCOPE_LOCK();`来锁定列表以防其改变(比如移除一个Ability). 每个域中的`ABILITYLIST_SCOPE_LOCK();`会增加`AbilityScopeLockCount`, 之后出域时会减量. 不要尝试在`ABILITYLIST_SCOPE_LOCK();`域中移除某个Ability(Ability删除函数会在内部检查`AbilityScopeLockCount`以防在列表锁定时移除Ability).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-asc-rm"></a>
#### 4.1.1 同步模式

`ASC`定义了三种不同的同步模式用于同步`GameplayEffect`, `GameplayTag`和`GameplayCue` - `Full`, `Mixed`和`Minimal`. `Attribute`由其`AttributeSet`同步.  

|同步模式|何时使用|描述|
|:-:|:-:|:-:|
|`Full`|单人|所有`GameplayEffect`都同步到客户端.|
|`Mixed`|多人, 玩家控制的Actor|`GameplayEffect`只同步到其所属客户端, 只有`GameplayTag`和`GameplayCue`同步到所有客户端.|
|`Minimal`|多人, AI控制的Actor|`GameplayEffect`从不同步到任何客户端, 只有`GameplayTag`和`GameplayCue`同步到所有客户端.|

**Note:** `Mixed`同步模式需要`OwnerActor`的`Owner`是`Controller`. `PlayerState`的`Owner`默认是`Controller`但是`Character`不是. 如果`OwnerActor`不是`PlayerState`时使用`Mixed`同步模式, 那么需要在`OwnerActor`中调用`SetOwner()`设置`Controller`.  

从4.24开始, 需要使用`PossessedBy()`设置新的`Controller`为`Pawn`的Owner.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-asc-setup"></a>
#### 4.1.2 设置和初始化

`ASC`一般在`OwnerActor`的构建函数中创建并且需要明确标记为Replicated. **这必须在C++中完成.**  

```c++
AGDPlayerState::AGDPlayerState()
{
	// Create Ability system component, and set it to be explicitly replicated
	AbilitySystemComponent = CreateDefaultSubobject<UGDAbilitySystemComponent>(TEXT("AbilitySystemComponent"));
	AbilitySystemComponent->SetIsReplicated(true);
	//...
}
```

`OwnerActor`和`AvatarActor`的`ASC`在服务端和客户端上均需初始化, 你应该在`Pawn`的`Controller`设置之后初始化(Possess之后), 单人游戏只需参考服务端的做法.  

对于玩家控制的Character且`ASC`位于`Pawn`, 我一般在服务端`Pawn`的`PossessedBy()`函数中初始化, 在客户端`PlayerController`的`AcknowledgePossession()`函数中初始化.  

```c++
void APACharacterBase::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	if (AbilitySystemComponent)
	{
		AbilitySystemComponent->InitAbilityActorInfo(this, this);
	}

	// ASC MixedMode replication requires that the ASC Owner's Owner be the Controller.
	SetOwner(NewController);
}
```

```c++
void APAPlayerControllerBase::AcknowledgePossession(APawn* P)
{
	Super::AcknowledgePossession(P);

	APACharacterBase* CharacterBase = Cast<APACharacterBase>(P);
	if (CharacterBase)
	{
		CharacterBase->GetAbilitySystemComponent()->InitAbilityActorInfo(CharacterBase, CharacterBase);
	}

	//...
}
```

对于玩家控制的Character且`ASC`位于`PlayerState`, 我一般在服务端`Pawn`的`PossessedBy()`函数中初始化, 在客户端PlayerController的`OnRep_PlayerState()`函数中初始化, 这确保了`PlayerState`存在于客户端上.  

```c++
void AGDHeroCharacter::PossessedBy(AController * NewController)
{
	Super::PossessedBy(NewController);

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC on the Server. Clients do this in OnRep_PlayerState()
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// AI won't have PlayerControllers so we can init again here just to be sure. No harm in initing twice for heroes that have PlayerControllers.
		PS->GetAbilitySystemComponent()->InitAbilityActorInfo(PS, this);
	}
	
	//...
}
```

```c++
void AGDHeroCharacter::OnRep_PlayerState()
{
	Super::OnRep_PlayerState();

	AGDPlayerState* PS = GetPlayerState<AGDPlayerState>();
	if (PS)
	{
		// Set the ASC for clients. Server does this in PossessedBy.
		AbilitySystemComponent = Cast<UGDAbilitySystemComponent>(PS->GetAbilitySystemComponent());

		// Init ASC Actor Info for clients. Server will init its `ASC` when it possesses a new Actor.
		AbilitySystemComponent->InitAbilityActorInfo(PS, this);
	}

	// ...
}
```

如果你遇到了错误消息`LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted Ability %s when not local!`, 那么就表明`ASC`没有在客户端中初始化.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gt"></a>
### 4.2 Gameplay Tags

`FGameplayTag`是由`GameplayTagManager`注册的形似`Parent.Child.Grandchild...`的层级Name, 这些标签对于分类和描述对象的状态非常有用, 例如, 如果某个Character处于眩晕状态, 我们可以给一个`State.Debuff.Stun`的`GameplayTag`.  

你会发现自己使用`GameplayTag`替换了过去使用布尔值或枚举值来编程, 并且需要对对象有无特定的`GameplayTag`做布尔逻辑判断.  

当给某个对象设置标签时, 如果它有`ASC`的话, 我们一般添加标签到`ASC`以与其交互. UAbilitySystemComponent执行`IGameplayTagAssetInterface`接口的函数来访问其拥有的`GameplayTag`.  

多个`GameplayTag`可被保存于一个`FGameplayTagContainer`中, 相比`TArray<FGameplayTag>`, 最好使用`GameplayTagContainer`, 因为`GameplayTagContainer`做了一些很有效率的优化. 因为标签是标准的`FName`, 所以当在项目设置(Project Setting)中启用`Fast Replication`后, 它们可以高效地打包进`FGameplayTagContainer`以用于同步. `Fast Replication`要求服务端和客户端有相同的`GameplayTag`列表, 这通常不是问题, 因此你应该启用该选项. `GameplayTagContainer`也可以返回`TArray<FGameplayTag>`以用于遍历.  

保存于`FGameplayTagCountContainer`中的`GameplayTag`有保存该`GameplayTag`实例数的`TagMap`. FGameplayTagCountContainer可能存有`TagMapCount`为0的`GameplayTag`, 你可能在Debug时遇到这种情况. 任何`HasTag()`或`HasMatchingTag()`或其他相似的函数会检查`TagMapCount`, 如果`GameplayTag`不存在或者其`TagMapCount`为0就会返回false.  

`GameplayTag`必须在`DefaultGameplayTag.ini`中提前定义, UE4编辑器在项目设置中提供了一个界面供开发者管理`GameplayTag`而无需手动编辑DefaultGameplayTag.ini, 该`GameplayTag`编辑器可以创建, 重命名, 搜索引用和删除`GameplayTag`.  

![GameplayTag Editor in Project Settings](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gameplaytageditor.png)  

搜索`GameplayTag`会弹出一个类似Reference Viewer的窗口来显示所有引用该`GameplayTag`的资源, 但这不会显示任何引用该`GameplayTag`的C++类.  

重命名`GameplayTag`会创建重定向, 因此仍引用原来`GameplayTag`的资源会重定向到新的`GameplayTag`. 如果可以的话, 我更倾向于创建新的`GameplayTag`, 手动更新所有引用到新的`GameplayTag`, 之后删除旧的`GameplayTag`以避免创建新的重定向.  

除了`Fast Replication`, `GameplayTag`编辑器可以选择填充普遍需要同步的`GameplayTag`以对其深度优化.  

如果`GameplayTag`由`GameplayEffect`添加, 那么其就是可同步的. `ASC`允许你添加不可同步的`LooseGameplayTag`且必须手动管理. 样例项目对`State.Dead`使用了`LooseGameplayTag`, 因此当生命值降为0时, 其所属客户端会立即响应. 重生时需要手动将`TagMapCount`设置回0, 当使用`LooseGameplayTag`时只能手动调整`TagMapCount`, 相比纯手动调整`TagMapCount`, 最好使用`UAbilitySystemComponent::AddLooseGameplayTag()`和`UAbilitySystemComponent::RemoveLooseGameplayTag()`.  

C++中获取`GameplayTag`引用:  

```c++
FGameplayTag::RequestGameplayTag(FName("Your.GameplayTag.Name"))
```

对于像获取父或子`GameplayTag`的高阶操作, 请查看`GameplayTagManager`提供的函数. 为了访问`GameplayTagManager`, 请引用`GameplayTagManager.h`并使用`UGameplayTagManager::Get().FunctionName`调用函数. 相比使用常量字符串进行操作和比较, `GameplayTagManager`实际上使用关系节点(父, 子等等)保存`GameplayTag`以获得更快的处理速度.  

`GameplayTag`和`GameplayTagContainer`有可选UPROPERTY宏`Meta = (Categories = "GameplayCue")`用于在蓝图中过滤标签而只显示父标签为`GameplayCue`的`GameplayTag`, 当你知道`GameplayTag`或`GameplayTagContainer`变量应该只用于`GameplayCue`时, 这将是非常有用的.  

作为选择, 有一单独的`FGameplayCueTag`结构体可以包裹`FGameplayTag`并且可以在蓝图中自动过滤`GameplayTag`而只显示父标签为`GameplayCue`的标签.  

如果你想过滤函数中的`GameplayTag`参数, 使用UFUNCTION宏`Meta = (GameplayTagFilter = "GameplayCue")`. `GameplayTagContainer`参数不能过滤, 如果你想编辑引擎来允许过滤`GameplayTagContainer`参数, 查看`SGameplayTagGraphPin::ParseDefaultValueData()`是如何从`Engine\Plugins\Editor\GameplayTagEditor\Source\GameplayTagEditor\Private\SGameplayTagGraphPin.cpp`中调用`FilterString = UGameplayTagManager::Get().GetCategoriesMetaFromField(PinStructType);`的, 还有是如何在`SGameplayTagGraphPin::GetListContent()`中将`FilterString`传递给`SGameplayTagWidget`的, `Engine\Plugins\Editor\GameplayTagEditor\Source\GameplayTagEditor\Private\S`GameplayTagContainer`GraphPin.cpp`中这些函数的`GameplayTagContainer`版本并没有检查Meta域属性和传递过滤器.  

样例项目广泛地使用了`GameplayTag`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gt-change"></a>
#### 4.2.1 响应Gameplay Tags的变化

`ASC`提供了一个委托(Delegate)用于在`GameplayTag`添加或移除时触发, 其中`EGameplayTagEventType`参数可以明确是该`GameplayTag`添加/移除还是其`TagMapCount`发生变化时触发.  

```c++
AbilitySystemComponent->RegisterGameplayTagEvent(FGameplayTag::RequestGameplayTag(FName("State.Debuff.Stun")), EGameplayTagEventType::NewOrRemoved).AddUObject(this, &AGDPlayerState::StunTagChanged);
```

回调函数拥有变化的`GameplayTag`参数和新的`TagCount`参数.  

```c++
virtual void StunTagChanged(const FGameplayTag CallbackTag, int32 NewCount);
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-a"></a>
### 4.3 Attribute

<a name="concepts-a-definition"></a>
#### 4.3.1 Attribute定义

`Attribute`是由[FGameplayAttributeData](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayAttributeData/index.html)结构体定义的浮点值, 其可以表示从角色生命值到角色等级再到一瓶药水的剂量的任何事物, 如果某项数值是属于某个Actor且游戏相关的, 你就应该考虑使用`Attribute`. `Attribute`一般应该只能由[GameplayEffect](#concepts-ge)修改, 这样`ASC`才能[预测(Predict)](#concepts-p)其改变.  

`Attribute`也可以由`AttributeSet`定义并存于其中. [AttributeSet](#concepts-as)用于同步那些标记为replication的`Attribute`. 参阅[AttributeSet](#concepts-as)部分来了解如何定义`Attribute`.  

**Tip**: 如果你不想某个`Attribute`显示在编辑器的`Attribute`列表, 可以使用`Meta = (HideInDetailsView)`属性宏.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-a-value"></a>
#### 4.3.2 BaseValue vs. CurrentValue

一个`Attribute`是由两个值 —— 一个`BaseValue`和一个`CurrentValue`组成的, `BaseValue`是`Attribute`的永久值而`CurrentValue`是`BaseValue`加上`GameplayEffect`给的临时修改值后得到的. 例如, 你的Character可能有一个`BaseValue`为600u/s的移动速度`Attribute`, 因为还没有`GameplayEffect`修改移动速度, 所以`CurrentValue`也是600u/s, 如果Character获得了一个临时50u/s的移动速度加成, 那么`BaseValue`仍然是600u/s而`CurrentValue`是600+50=650u/s, 当该移动速度加成消失后, `CurrentValue`就会变回`BaseValue`的600u/s.  

初识GAS的新手经常将`BaseValue`误认为`Attribute`的最大值并以这样的认识去编程, 这是错误的, 可以修改或引用的Ability/UI中的`Attribute`最大值应该是另外单独的`Attribute`. 对于硬编码的最大值和最小值, 有一种方法是使用可以设置最大值和最小值的`FAttributeMetaData`定义一个DataTable, 但是Epic在该结构体上的注释称之为"work in progress", 详见`AttributeSet.h`. 为了避免这种疑惑, 我建议引用在Ability或UI中的最大值应该单独定义`Attribute`, 只用于限制(Clamp)`Attribute`大小的硬编码最大值和最小值应该在`AttributeSet`中定义为硬编码浮点值. 关于限制(Clamp)`Attribute`值的问题在[PreAttributeChange()](#concepts-as-preattributechange)中讨论了CurrentValue的修改, 在[PostGameplayEffectExecute()](#concepts-as-postgameplayeffectexecute)中讨论了`GameplayEffect`对`BaseValue`的修改.  

`即刻(Instant)GameplayEffect`可以永久性的修改`BaseValue`, 而`持续(Duration)`和`无限(Infinite)GameplayEffect`可以修改CurrentValue. 周期性(Periodic)`GameplayEffect`被视为`即刻(Instant)GameplayEffect`并且可以修改`BaseValue`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-a-meta"></a>
#### 4.3.3 元(Meta)Attribute

一些`Attribute`被视为占位符, 其是用于预计和`Attribute`交互的临时值, 这些`Attribute`被叫做`Meta Attribute`. 例如, 我们通常定义伤害值为`Meta Attribute`, 使用伤害值`Meta Attribute`作为占位符, 而不是使用`GameplayEffect`直接修改生命值`Attribute`, 使用这种方法, 伤害值就可以在[GameplayEffectExecutionCalculation](#concepts-ge-ec)中由buff和debuff修改, 并且可以在`AttributeSet`中进一步操作, 例如, 在最终将生命值减去伤害值之前, 要将伤害值减去当前的护盾值. 伤害值`Meta Attribute`在`GameplayEffect`之间不是持久化的, 并且可以被任何一方重写. `Meta Attribute`一般是不可同步的.  

`Meta Attribute`对于在"我们应该造成多少伤害?"和"我们该如何处理伤害值?"这种问题之中的伤害值和治疗值做了很好的解构, 这种解构意味着`GameplayEffect`和`ExecutionCalculation`无需了解目标是如何处理伤害值的. 继续看伤害值的例子, `GameplayEffect`确定造成多少伤害, 之后`AttributeSet`决定如何使用该伤害值, 不是所有的Character都有相同的`Attribute`, 特别是使用了`AttributeSet`子类的话, `AttributeSet`基类可能只有一个生命值`Attribute`, 但是它的子类可能增加了一个护盾值`Attribute`, 拥有护盾值`Attribute`的子类`AttributeSet`可能会以不同于`AttributeSet`基类的方式分配收到的伤害.  

尽管`Meta Attribute`是一个很好的设计模式, 但其并不是强制使用的. 如果你只有一个用于所有伤害实例的`Execution Calculation`和一个所有Character共用的`AttributeSet`类, 那么你就可以在`Exeuction Calculation`中分配伤害到生命, 护盾等等, 并直接修改那些`Attribute`, 这种方式你只会丢失灵活性, 但总体上并无大碍.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-a-changes"></a>
#### 4.3.4 响应Attribute变化

为了监听`Attribute`何时变化以便更新UI和其他游戏逻辑, 可以使用`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute `Attribute`)`, 该函数返回一个委托(Delegate), 你可以将其绑定一个当`Attribute`变化时需要自动调用的函数. 该委托提供一个`FOnAttributeChangeData`参数, 其中有`NewValue`, `OldValue`和`FGameplayEffectModCallbackData`. **Note**: `FGameplayEffectModCallbackData`只能在服务端上设置.  

```c++
AbilitySystemComponent->GetGameplayAttributeValueChangeDelegate(AttributeSetBase->GetHealthAttribute()).AddUObject(this, &AGDPlayerState::HealthChanged);

virtual void HealthChanged(const FOnAttributeChangeData& Data);
```

样例项目将其绑定到了`GDPlayerState`用于更新HUD, 当生命值下降为0时, 也可以响应玩家死亡.  

样例项目中有一个将上述逻辑包裹进`ASyncTask`的自定义蓝图节点, 其在`UI_HUD(UMG Widget)`中用于更新生命值, 魔法值和耐力值. 该AsyncTask会一直响应直到手动调用`EndTask()`, 就像在UMG Widget的`Destruct`事件中调用那样. 参阅`AsyncTaskAttributeChanged.h/cpp`.  

![Listen for Attribute Change BP Node](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/attributechange.png) 

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-a-derived"></a>
#### 4.3.5 自动推导Attribute

为了使一个`Attribute`的部分或全部值继承自一个或更多`Attribute`, 可以使用基于一个或多个`Attribute`或[MMC](#concepts-ge-mmc) [Modifiers](#concepts-ge-mods)的`无限(Infinite)GameplayEffect`. 当自动推导`Attribute`依赖的某个`Attribute`更新时它也会自动更新.  

在自动推导`Attribute`上的所有`Modifier(Modifier)`形成的最终公式和`Modifier Aggregators`的公式是一样的. 如果你需要计算式要按一定的顺序进行, 在`MMC`中做就是了.  

```c++
((CurrentValue + Additive) * Multiplicitive) / Division
```

**Note**: 如果在PIE中打开多个窗口, 你需要在编辑器首选项中禁用`Run Under One Process`, 否则当自动推导`Attribute`所依赖的`Attribute`更新时, 除了第一个窗口外其不会更新.  

在这个例子中, 我们有一个`无限(Infinite)GameplayEffect`, 其从TestAttrB和TestAttrC `Attribute`以`TestAttrA = (TestAttrA + TestAttrB) * ( 2 * TestAttrC)`公式继承得到TestAttrA, 每次TestAttrB和TestAttrC更新时, TestAttrA都会自动重新计算.  

![Derived Attribute Example](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/derivedattribute.png)  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as"></a>
### 4.4 AttributeSet

<a name="concepts-as-definition"></a>
#### 4.4.1 定义AttributeSet

`AttributeSet`用于定义, 保存以及管理对`Attribute`的修改. 开发者应该继承[UAttributeSet](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAttributeSet/index.html). 在OwnerActor的构造函数中创建`AttributeSet`会自动注册到其`ASC`. **这必须在C++中完成.**  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design"></a>
#### 4.4.2 设计AttributeSet

一个`ASC`可能有一个或多个`AttributeSet`, `AttributeSet`消耗的内存微不足道, 使用多少`AttributeSet`是留给开发人员决定的.  

有种方案是设置一个单一且巨大的`AttributeSet`, 共享于游戏中的所有Actor, 并且只使用需要的`Attribute`, 忽略不用的`Attribute`.  

作为选择, 你可以使用多个`AttributeSet`来表示按需添加到Actor的`Attribute`分组, 例如, 你可以有一个生命相关的`AttributeSet`, 一个魔法相关的`AttributeSet`等等. 在MOBA游戏中, 英雄可能需要魔法, 但是小兵并不需要, 因此英雄就需要魔法`AttributeSet`而小兵则不需要.  

另外, 继承`AttributeSet`的另一种意义是可以选择一个Actor可以有哪些`Attribute`. `Attribute`在内部被引用为`AttributeSetClassName.AttributeName`, 当你继承`AttributeSet`时, 所有父类的`Attribute`仍将保留父类名作为前缀.  

尽管可以拥有多个`AttributeSet`, 但是不应该在同一`ASC`中拥有多个同一类的`AttributeSet`, 如果在同一`ASC`中有多个同一类的`AttributeSet`, `ASC`就不知道该使用哪个`AttributeSet`而随机选择一个.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design-subcomponents"></a>
##### 4.4.2.1 使用单独Attribute的子组件

假设你在某个Pawn上有多个可被损害的组件, 像可被独立损害的护甲片, 如果可以明确可被损害组件的最大数量, 我建议把多个生命值`Attribute`放到一个`AttributeSet`中 —— DamageableCompHealth0, DamageableCompHealth1, 等等, 以表示这些可被损害组件在逻辑上的"slot", 在可被损害组件的类实例中, 指定可以被`GameplayAbility`和[Execution](#concepts-ge-ec)读取的带slot编号的`Attribute`来表明该应用伤害值到哪个`Attribute`. 如果Pawn当前拥有0个或少于最大数量的可损害组件也无妨, 因为`AttributeSet`拥有一个`Attribute`, 并不意味着必须要使用它, 未使用的`Attribute`只占用很少的内存.  

如果每个子组件都需要很多`Attribute`且子组件的数量可以是无限的, 或者子组件可以分离被其他玩家使用(比如武器), 或者出于其他原因上述方法不适用于你, 那么我建议就不要使用`Attribute`, 而是在组件中保存普通的浮点数. 参阅[Item Attribute](#concepts-as-design-itemattributes).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design-addremoveruntime"></a>
##### 4.4.2.2 运行时添加和移除AttributeSet

`AttributeSet`可以在运行时从`ASC`上添加和移除, 然而移除`AttributeSet`是很危险的, 例如, 如果某个`AttributeSet`在客户端上移除早于服务端, 而某个`Attribute`的变化又同步到了客户端, 那么`Attribute`就会因为找不到`AttributeSet`而使游戏崩溃.  

武器添加到Inventory:  

```c++
AbilitySystemComponent->SpawnedAttribute.AddUnique(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

武器从Inventory移除:  

```c++
AbilitySystemComponent->SpawnedAttribute.Remove(WeaponAttributeSetPointer);
AbilitySystemComponent->ForceReplication();
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design-itemattributes"></a>
##### 4.4.2.3 Item Attribute(武器弹药)

有几种方法可以实现带有`Attribute`(武器弹药, 盔甲耐久等等)的可装备物品, 所有这些方法都直接在物品中存储数据, 这对于在生命周期中可以被多个玩家装备的物品来说是必须的.  

1. 在物品中使用普通的浮点数(推荐).
2. 在物品中使用单独的`AttributeSet`.
3. 在物品中使用单独的`ASC`.

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design-itemattributes-plainfloats"></a>
###### 4.4.2.3.1 在物品中使用普通浮点数

在物品类实例中存储普通浮点数而不是`Attribute`, Fortnite和[GASShooter](https://github.com/tranek/GASShooter)就是这样处理枪械子弹的, 对于枪械, 在其实例中存储可同步的浮点数(COND_OwnerOnly), 比如最大弹匣量, 当前弹匣中弹药量, 剩余弹药量等等, 如果枪械需要共享剩余弹药量, 那么就将剩余弹药量移到Character中共享的弹药`AttributeSet`里作为一个`Attribute`(换弹Ability可以使用一个`Cost GE`从剩余弹药量中填充枪械的弹匣弹药量浮点). 因为没有为当前弹匣弹药量使用`Attribute`, 所以需要重写`UGameplayAbility`中的一些函数来检查和应用枪械中浮点数的花销(cost). 当授予Ability时将枪械在[GameplayAbilitySpec](https://github.com/tranek/GASDocumentation#concepts-ga-spec)中转换为`SourceObject`, 这意味着可以在Ability中访问授予Ability的枪械.  

为了防止在全自动射击过程中枪械会反向同步弹药量并扰乱本地弹药量(译者注: 通俗解释就是因为存在同步延迟且在连续射击这一高同步过程中, 所以客户端的弹药量会来不及和服务端同步, 造成弹药量减少后又突然变多的现象.), 如果玩家拥有`IsFiring`的`GameplayTag`, 就在PreReplication()中禁用同步, 本质上是要在其中做自己的本地预测.  

```c++
void AGSWeapon::PreReplication(IRepChangedPropertyTracker& ChangedPropertyTracker)
{
	Super::PreReplication(ChangedPropertyTracker);

	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, PrimaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
	DOREPLIFETIME_ACTIVE_OVERRIDE(AGSWeapon, SecondaryClipAmmo, (IsValid(AbilitySystemComponent) && !AbilitySystemComponent->HasMatchingGameplayTag(WeaponIsFiringTag)));
}
```

好处:  

1. 避免了使用`AttributeSet`的局限(见下).

局限:  

1. 不能使用现有的`GameplayEffect`工作流(弹药使用的Cost GEs等等).
2. 要求重写UGameplayAbility中的关键函数来检查和应用枪械中浮点数的花销(Cost).

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design-itemattributes-attributeset"></a>
###### 4.4.2.3.2 在物品中使用AttributeSet

在物品中使用单独的`AttributeSet`可以实现[将其添加到玩家的Inventory](#concepts-as-design-addremoveruntime), 但还是有一定的局限性. 较早版本的[GASShooter](https://github.com/tranek/GASShooter)中的武器弹药是使用的这种方法, 武器类在其自身存储诸如最大弹匣量, 当前弹匣弹药量, 剩余弹药量等等到一个`AttributeSet`, 如果枪械需要共享剩余弹药量, 那么就将剩余弹药量移到Character中共享的弹药`AttributeSet`里. 当服务端上某个武器添加到玩家的Inventory后, 该武器会将它的`AttributeSet`添加到玩家的`ASC::SpawnedAttribute`, 之后服务端会将其同步下发到客户端, 如果该武器从Inventory中移除, 它也会将其`AttributeSet`从`ASC::SpawnedAttribute`中移除.  

当`AttributeSet`存于除了OwnerActor之外的对象上时(对于某个武器来说), 会得到一些关于`AttributeSet`的编译错误, 解决办法是在BeginPlay()中构建`AttributeSet`而不是在构造函数中, 并在武器类中实现`IAbilitySystemInterface`(当你添加武器到玩家Inventory时设置`ASC`的指针).  

```c++
void AGSWeapon::BeginPlay()
{
	if (!AttributeSet)
	{
		AttributeSet = NewObject<UGSWeaponAttributeSet>(this);
	}
	//...
}
```

你可以查看[较早版本的GASShooter](https://github.com/tranek/GASShooter/tree/df5949d0dd992bd3d76d4a728f370f2e2c827735)来实际地体会这种方案.  

好处:  
1. 可以使用已有的`GameplayAbility`和`GameplayEffect`工作流(弹药使用的Cost GEs等等).
2. 对于很小的物品集可以快速设置

局限:  
1. 必须为每个武器类型创建新的`AttributeSet`类, `ASC`实际上只能有一个该类的`AttributeSet`实例, 因为对`Attribute`的修改会在`ASC`的SpawnedAttribute数组中寻找其第一个`AttributeSet`类实例, 其他相同的`AttributeSet`类实例则会被忽略.  
2. 和第1条同样的原因(每个`AttributeSet`类一个`AttributeSet`实例), 在玩家的Inventory中每种武器类型只能有一个.
3. 移除`AttributeSet`是很危险的. 在GASShooter中, 如果玩家因为火箭弹而自杀, 玩家会立即从其Inventory中移除火箭弹发射器(包括其在`ASC`中的`AttributeSet`), 当服务端同步火箭弹发射器的弹药`Attribute`改变时, 由于`AttributeSet`在客户端`ASC`上不复存在而使游戏崩溃.

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-design-itemattributes-asc"></a>
###### 4.4.2.3.3 在物品中使用单独的ASC

在每个物品上都创建一个`AbilitySystemComponent`是种很极端的方案. 我还没有亲自做过这种方案, 在其他地方也没见过. 这种方案应该会花费相当的开发成本才能正常使用.  

> Is it viable to have several AbilitySystemComponents which have the same owner but different avatars (e.g. on pawn and weapon/items/projectiles with Owner set to PlayerState)?

> The first problem I see there would be implementing the IGameplayTagAssetInterface and IAbilitySystemInterface on the owning Actor. The former may be possible: just aggregate the tags from all all ASCs (but watch out -HasAlMatchingGameplayTag may be met only via cross ASC aggregation. It wouldn't be enough to just forward that calls to each ASC and OR the results together). But the later is even trickier: which ASC is the authoritative one? If someone wants to apply a GE -which one should receive it? Maybe you can work these out but this side of the problem will be the hardest: owners will multiple ASCs beneath them.

> Separate ASCs on the pawn and the weapon can make sense on its own though. E.g, distinguishing between tags the describe the weapon vs those that describe the owning pawn. Maybe it does make sense that tags granted to the weapon also “apply” to the owner and nothing else (E.g, Attribute and GEs are independent but the owner will aggregate the owned tags like I describe above). This could work out, I am sure. But having multiple ASCs with the same owner may get dicey.

*[community questions #6](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)中来自Epic的Dave Ratti的回答.*  

好处: 
1. 可以使用已有的`GameplayAbility`和`GameplayEffect`工作流(弹药使用的Cost GEs等等).
2. 可以复用`AttributeSet`类(每个武器的`ASC`中各一个).

局限:  
1. 未知的开发成本.
2. 甚至方案可行么?

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-attributes"></a>
#### 4.4.3 定义Attribute

**Attribute只能使用C++在AttributeSet头文件中定义.** 建议把下面这个宏块加到每个`AttributeSet`头文件的顶部, 其会自动为每个`Attribute`生成getter和setter函数.  

```c++
// Uses macros from AttributeSet.h
#define ATTRIBUTE_ACCESSORS(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_PROPERTY_GETTER(ClassName, PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_GETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_SETTER(PropertyName) \
	GAMEPLAYATTRIBUTE_VALUE_INITTER(PropertyName)
```

一个可同步的生命值`Attribute`可能像下面这样定义:  

```c++
UPROPERTY(BlueprintReadOnly, Category = "Health", ReplicatedUsing = OnRep_Health)
FGameplayAttributeData Health;
ATTRIBUTE_ACCESSORS(UGDAttributeSetBase, Health)
```

同样在头文件中定义OnRep函数:  

```c++
UFUNCTION()
virtual void OnRep_Health(const FGameplayAttributeData& OldHealth);
```

`AttributeSet`的.cpp文件应该用预测系统(Prediction System)使用的`GAMEPLAYATTRIBUTE_REPNOTIFY`宏填充OnRep函数:  

```c++
void UGDAttributeSetBase::OnRep_Health(const FGameplayAttributeData& OldHealth)
{
	GAMEPLAYATTRIBUTE_REPNOTIFY(UGDAttributeSetBase, Health, OldHealth);
}
```

最后, `Attribute`需要添加到`GetLifetimeReplicatedProps`:  

```c++
void UGDAttributeSetBase::GetLifetimeReplicatedProps(TArray<FLifetimeProperty>& OutLifetimeProps) const
{
	Super::GetLifetimeReplicatedProps(OutLifetimeProps);

	DOREPLIFETIME_CONDITION_NOTIFY(UGDAttributeSetBase, Health, COND_None, REPNOTIFY_Always);
}
```

`REPTNOTIFY_Always`用于设置OnRep函数在客户端值已经与服务端同步的值相同的情况下触发(因为有预测), 默认设置下, 客户端值与服务端同步的值相同时, OnRep函数是不会触发的.  

如果`Attribute`无需像`Meta Attribute`那样同步, 那么`OnRep`和`GetLifetimeReplicatedProps`步骤可以跳过.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-init"></a>
#### 4.4.4 初始化Attribute

有多种方法可以初始化`Attribute`(将BaseValue和CurrentValue设置为某初始值). Epic建议使用`即刻(Instant)GameplayEffect`, 这也是样例项目使用的方法.  

查看样例项目的GE_HeroAttribute蓝图来了解如何创建`即刻(Instant)GameplayEffect`以初始化`Attribute`, 该`GameplayEffect`应用是写在C++中的.  

如果在定义`Attribute`时使用了ATTRIBUTE_ACCESSORS宏, 那么在`AttributeSet`中会自动为每个`Attribute`生成一个初始化函数.  

```c++
// InitHealth(float InitialValue) is an automatically generated function for an Attribute 'Health' defined with the `ATTRIBUTE_ACCESSORS` macro
AttributeSet->InitHealth(100.0f);
```

查看`AttributeSet.h`获取更多初始化`Attribute`的方法.  

**Note**: 4.24之前, FAttributeSetInitterDiscreteLevels不能和FGameplayAttributeData协同使用, 它在`Attribute`是原生浮点数时创建, 并且会和FGameplayAttributeData不是Plain Old Data(POD)时冲突. 该问题在4.24中修复([https://issues.unrealengine.com/issue/UE-76557.](https://issues.unrealengine.com/issue/UE-76557)).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-preattributechange"></a>
#### 4.4.5 PreAttributeChange()

`PreAttributeChange(const FGameplayAttribute& Attribute, float& NewValue)`是`AttributeSet`中的主要函数之一, 其在修改发生前响应`Attribute`的CurrentValue变化, 其是通过引用参数NewValue限制(Clamp)CurrentValue即将进行的修改的理想位置.  

例如像样例项目那样限制移动速度`Modifier`:  
```c++
if (Attribute == GetMoveSpeedAttribute())
{
	// Cannot slow less than 150 units/s and cannot boost more than 1000 units/s
	NewValue = FMath::Clamp<float>(NewValue, 150, 1000);
}
```

`GetMoveSpeedAttribute()`函数是由我们在`AttributeSet.h`中添加的宏块创建的([定义Attribute](#concepts-as-attributes)).  

`PreAttributeChange()`可以被`Attribute`的任何修改触发, 无论是使用`Attribute`的setter(由`AttributeSet.h`中的宏块定义([定义Attribute](#concepts-as-attributes)))还是使用[GameplayEffect](#concepts-ge).  

**Note**: 在这里做的任何限制都不会永久性地修改`ASC`中的`Modifier`, 只会修改查询`Modifier`的返回值, 这意味着像[GameplayEffectExecutionCalculations](#concepts-ge-ec)和[ModifierMagnitudeCalculations](#concepts-ge-mmc)这种自所有`Modifier`重新计算CurrentValue的函数需要再次执行限制(Clamp)操作.  

**Note**: Epic对于PreAttributeChange()的注释说明不要将该函数用于游戏逻辑事件, 而主要在其中做限制操作. 对于修改`Attribute`的游戏逻辑事件的建议位置是`UAbilitySystemComponent::GetGameplayAttributeValueChangeDelegate(FGameplayAttribute Attribute)`([响应Attribute变化](#concepts-a-changes)).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-postgameplayeffectexecute"></a>
#### 4.4.6 PostGameplayEffectExecute()

`PostGameplayEffectExecute(const FGameplayEffectModCallbackData & Data)`仅在`即刻(Instant)GameplayEffect`对`Attribute`的BaseValue修改之后触发, 当[GameplayEffect](#concepts-ge)对其修改时, 这就是一个处理更多`Attribute`操作的有效位置.  

例如, 在样例项目中, 我们在这里从生命值`Attribute`中减去了最终的伤害值`Meta Attribute`, 如果有护盾值`Attribute`的话, 我们也会在减除生命值之前从护盾值中减除伤害值. 样例项目也在这里应用了被击打反应动画, 显示浮动的伤害数值和为击杀者分配经验值和赏金. 通过设计, 伤害值`Meta Attribute`总是会传递给`即刻(Instant)GameplayEffect`而不是Attribute Setter.  

其他只会由`即刻(Instant)GameplayEffect`修改BaseValue的`Attribute`, 像魔法值和耐力值, 也可以在这里被限制为其相应的最大值`Attribute`.  

**Note**: 当PostGameplayEffectExecute()被调用时, 对`Attribute`的修改已经发生, 但是还没有被同步回客户端, 因此在这里限制值不会造成对客户端的二次同步, 客户端只会接收到限制后的值.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-as-onattributeaggregatorcreated"></a>
#### 4.4.7 OnAttributeAggregatorCreated()

`OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator)`会在Aggregator为集合中的某个`Attribute`创建时触发, 它允许[FAggregatorEvaluateMetaData](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FAggregatorEvaluateMetaData/index.html)的自定义设置, `AggregatorEvaluateMetaData`是Aggregator基于所有应用的`Modifier(Modifier)`评估`Attribute`的CurrentValue的. 默认情况下, AggregatorEvaluateMetaData只由Aggregator用于确定哪些[Modifier](#concepts-ge-mods)是满足条件的, 以MostNegativeMod_AllPositiveMods为例, 其允许所有正(Positive)`Modifier`但是限制负(Negative)`Modifier`(仅最负的那一个), 这在Paragon中只允许将最负移动速度减速效果应用到玩家, 而不用管应用所有正移动速度buff时有多少负移动效果. 不满足条件的`Modifier`仍存于`ASC`中, 只是不被总合进最终的CurrentValue, 一旦条件改变, 它们之后就可能满足条件, 就像如果最负`Modifier`过期后, 下一个最负`Modifier`(如果存在的话)就是满足条件的.  

为了在只允许最负`Modifier`和所有正`Modifier`的例子中使用`AggregatorEvaluateMetaData`:  

```c++
virtual void OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const override;

void UGSAttributeSetBase::OnAttributeAggregatorCreated(const FGameplayAttribute& Attribute, FAggregator* NewAggregator) const
{
	Super::OnAttributeAggregatorCreated(Attribute, NewAggregator);

	if (!NewAggregator)
	{
		return;
	}

	if (Attribute == GetMoveSpeedAttribute())
	{
		NewAggregator->EvaluationMetaData = &FAggregatorEvaluateMetaDataLibrary::MostNegativeMod_AllPositiveMods;
	}
}
```

你的自定义`AggregatorEvaluateMetaData`应该作为静态变量添加到`FAggregatorEvaluateMetaDataLibrary`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge"></a>
### 4.5 Gameplay Effects

<a name="concepts-ge-definition"></a>
#### 4.5.1 定义GameplayEffect

[GameplayEffect(GE)](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffect/index.html)是Ability修改其自身和其他[Attribute](#concepts-a)和[GameplayTag](#concepts-gt)的容器, 其可以立即修改`Attribute`(像伤害或治疗)或应用长期的状态buff/debuff(像移动速度加速或眩晕). UGameplayEffect只是一个定义单一游戏效果的数据类, 不应该在其中添加额外的逻辑. 设计师一般会创建很多UGameplayEffect的子类蓝图.  

`GameplayEffect`通过[Modifier](#concepts-ge-mods)和[Execution(GameplayEffectExecutionCalculation)](#concepts-ge-ec)修改`Attribute`.  

`GameplayEffect`有三种持续类型: `即刻(Instant)`, `持续(Duration)`和`无限(Infinite)`.  

额外地, `GameplayEffect`可以添加/执行[GameplayCue](#concepts-gc), `即刻(Instant)GameplayEffect`可以调用`GameplayCue`, `GameplayTag`中的Execute而`持续(Duration)`或`无限(Infinite)`可以调用`GameplayCue`, `GameplayTag`中的Add和Remove.  

|类型|GameplayCue事件|何时使用|
|:-:|:-:|:-:|
|即刻(Instant)|Execute|对于`Attribute`中BaseValue的永久性的立即修改. `GameplayTag`不会被应用, 哪怕是一帧.|
|持续(Duration)|Add & Remove|对于`Attribute`中CurrentValue的临时修改和当`GameplayEffect`过期或手动移除时, 应用将要被移除的`GameplayTag`. 持续时间是在UGameplayEffect类/蓝图中明确的.|
|无限(Infinite)|Add & Remove|对于`Attribute`中CurrentValue的临时修改和当`GameplayEffect`移除时, 应用将要被移除的`GameplayTag`. 该类型自身永不过期且必须由某个Ability或`ASC`手动移除.|

`持续(Duration)`和`无限(Infinite)GameplayEffect`可以选择应用周期性的Effect, 其每过X秒(由周期定义)就应用一次`Modifier`和Execution, 当周期性的Effect修改`Attribute`的BaseValue和执行`GameplayCue`时就被视为`即刻(Instant)GameplayEffect`, 这种类型的Effect对于像随时间推移的持续伤害(damage over time, DOT)很有用. **Note**: 周期性的Effect不能被[预测](#concepts-p).  

如果`持续(Duration)`和`无限(Infinite)GameplayEffect`进行中的标签需求(Ongoing Tag Requirements)未满足/满足的话([Gameplay Effect Tags](#concepts-ge-tags)), 那么它们在应用后就可以被暂时的关闭和打开, 关闭`GameplayEffect`会移除其`Modifier`和已应用`GameplayTag`的效果, 但是不会移除该`GameplayEffect`, 重新打开`GameplayEffect`会重新应用其`Modifier`和`GameplayTag`.  

如果你需要手动重新计算某个`持续(Duration)`或`无限(Infinite)GameplayEffect`的`Modifier`(假设有一个使用非`Attribute`数据的`MMC`), 可以使用和`UAbilitySystemComponent::ActiveGameplayEffect.GetActiveGameplayEffect(ActiveHandle).Spec.GetLevel()`相同的Level`调用UAbilitySystemComponent::ActiveGameplayEffect.SetActiveGameplayEffectLevel(FActiveGameplayEffectHandle ActiveHandle, int32 NewLevel)`. 当支持(backing)`Attribute`更新时, 基于支持(backing)`Attribute`的`Modifier`会自动更新. SetActiveGameplayEffectLevel()更新`Modifier`的关键函数是:  

```c++
MarkItemDirty(Effect);
Effect.Spec.CalculateModifierMagnitudes();
// Private function otherwise we'd call these three functions without needing to set the level to what it already is
UpdateAllAggregatorModMagnitudes(Effect);
```

`GameplayEffect`一般是不实例化的, 当Ability或`ASC`想要应用`GameplayEffect`时, 其会从`GameplayEffect`的`ClassDefaultObject`创建一个`GameplayEffectSpec`, 之后成功应用的`GameplayEffectSpec`会被添加到一个名为`FActiveGameplayEffect`的新结构体, 其是`ASC`在名为`ActiveGameplayEffect`的特殊结构体容器中追踪的内容.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-applying"></a>
#### 4.5.2 应用GameplayEffect

`GameplayEffect`可以被[GameplayAbility](#concepts-ga)和`ASC`中的多个函数应用, 其通常是`ApplyGameplayEffectTo`的形式, 不同的函数本质上都是最终在目标上调用`UAbilitySystemComponent::ApplyGameplayEffectSpecToSelf()`的方便函数.  

为了在`GameplayAbility`之外应用`GameplayEffect`, 例如对于某个投掷物, 你就需要获取到目标的`ASC`并使用它的函数之一来`ApplyGameplayEffectToSelf`.  

你可以绑定`持续(Duration)`或`无限(Infinite)GameplayEffect`的委托来监听其应用到`ASC`:  

```c++
AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf.AddUObject(this, &APACharacterBase::OnActiveGameplayEffectAddedCallback);
```

回调函数:  

```c++
virtual void OnActiveGameplayEffectAddedCallback(UAbilitySystemComponent* Target, const FGameplayEffectSpec& SpecApplied, FActiveGameplayEffectHandle ActiveHandle);
```

服务端总是会调用该函数而不管同步模式是什么, `Autonomous Proxy`只会在`Full`和`Mixed`同步模式下对于同步的`GameplayEffect`调用该函数, `Simulated Proxy`只会在`Full`同步模式下调用该函数.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-removing"></a>
#### 4.5.3 移除GameplayEffect

`GameplayEffect`可以被[GameplayAbility](#concepts-ga)和`ASC`中的多个函数移除, 其通常是`RemoveActiveGameplayEffect`的形式, 不同的函数本质上都是最终在目标上调用`FActiveGameplayEffectContainer::RemoveActiveEffects()`的方便函数.  

为了在`GameplayAbility`之外移除`GameplayEffect`, 你就需要获取到该目标的`ASC`并使用它的函数之一来`RemoveActiveGameplayEffect`.  

你可以绑定`持续(Duration)`或`无限(Infinite)GameplayEffect`的委托来监听其应用到`ASC`:  

```c++
AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate().AddUObject(this, &APACharacterBase::OnRemoveGameplayEffectCallback);
```

回调函数:  

```c++
virtual void OnRemoveGameplayEffectCallback(const FActiveGameplayEffect& EffectRemoved);
```

服务端总是会调用该函数而不管同步模式是什么, `Autonomous Proxy`只会在Full和Mixed同步模式下对于可同步的`GameplayEffect`调用该函数, `Simulated Proxy`只会在Full同步模式下调用该函数.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-mods"></a>
#### 4.5.4 GameplayEffectModifier

`Modifier`可以修改`Attribute`并且是唯一可以[预测性](#concepts-p)修改`Attribute`的方法. 一个`GameplayEffect`可以有0个或多个`Modifier`, 每个`Modifier`通过某个指定的操作只能修改一个`Attribute`.  

|操作|描述|
|:-:|:-:|
|Add|将`Modifier`指定的`Attribute`加上计算结果. 使用负数以实现减法操作.|
|Multiply|将`Modifier`指定的`Attribute`乘以计算结果.|
|Divide|将`Modifier`指定的`Attribute`除以计算结果.|
|Override|使用计算结果覆盖`Modifier`指定的`Attribute`.|

`Attribute`的CurrentValue是其所有`Modifier`与其BaseValue计算并总合后的结果, 像下面这样的`Modifier`总合公式被定义在`GameplayEffectAggregator.cpp`中的`FAggregatorModChannel::EvaluateWithBase`:  

```c++
((InlineBaseValue + Additive) * Multiplicitive) / Division
```

Override`Modifier`会优先覆盖最后应用的`Modifier`得出的最终值.  

**Note**: 对于基于百分比的修改, 确保使用`Multiply`操作以使其在加法操作之后.  

**Note**: [预测(Prediction)](#concepts-p)对于百分比修改有些问题.  

有四种类型的`Modifier`: `ScalableFloat`, `AttributeBased`, `CustomCalculationClass`, 和 `SetByCaller`, 它们全都生成一些浮点数, 用于之后基于各自的操作修改指定`Modifier`的`Attribute`.  

|Modifier类型|描述|
|:-:|:-:|
|Scalable Float|FScalableFloat结构体可以指向某个横向为变量, 纵向为等级的Data Table, `Scalable Float`会以Ability的当前等级自动读取指定Data Table的某行值(或者在[GameplayEffectSpec](#concepts-ge-spec)中重写的不同等级), 该值可以被系数处理, 如果没有指定Data Table/Row, 那么该值就会被视为1, 因此系数就可以被用来在所有等级硬编码为一个单一值.![ScalableFloat](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/scalablefloats.png)|	
|Attribute Based|`Attribute` Based`Modifier`将CurrentValue或BaseValue视为Source(`GameplayEffectSpec`的创建者)或Target(`GameplayEffectSpec`的接收者)的支持(Backing)`Attribute`, 可以使用系数和前后系数之和来修改它. `Snapshotting`意味着当`GameplayEffectSpec`创建时支持(Backing)`Attribute`被捕获(Captured), 而`no snapshotting`意味着当`GameplayEffectSpec`应用时该`Attribute`被捕获.|
|Custom Calculation Class|`Custom Calculation Class`为复杂的`Modifier`提供了最大的灵活性, 该`Modifier`使用了[ModifierMagnitudeCalculation](#concepts-ge-mmc)类, 且可以使用系数和前后系数之和处理浮点值结果.|
|Set By Caller|`SetByCaller`Modifier是运行时由Ability或`GameplayEffectSpec`的创建者于`GameplayEffect`之外设置的值, 例如, 如果你想让伤害值随玩家蓄力技能的长短而变化, 那么就需要使用`SetByCaller`. `SetByCaller`本质上是存于`GameplayEffectSpec`中的`TMap<FGameplayTag, float>`, `Modifier`只是告知`Aggregator`去寻找与提供的`GameplayTag`相关联的`SetByCaller`值. `Modifier`使用的`SetByCaller`只能使用该概念的`GameplayTag`形式, `FName`形式在此处不适用. 如果`Modifier`被设置为`SetByCaller`, 但是带有正确`GameplayTag`的`SetByCaller`在`GameplayEffectSpec`中不存在, 那么游戏会抛出一个运行时错误并返回0, 这可能在`Divide`操作中造成问题. 参阅[SetByCallers](#concepts-ge-spec-setbycaller)获取更多关于如何使用`SetByCaller`的信息.|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-mods-multiplydivide"></a>
##### 4.5.4.1 Multiply和Divide Modifier

默认情况下, 所有的`Multiply`和`Divide`Modifier在对`Attribute`的BaseValue乘除前都会先加到一起.  

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Additive = SumMods(Mods[EGameplayModOp::Additive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Additive), Parameters);
	float Multiplicitive = SumMods(Mods[EGameplayModOp::Multiplicitive], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Multiplicitive), Parameters);
	float Division = SumMods(Mods[EGameplayModOp::Division], GameplayEffectUtilities::GetModifierBiasByModifierOp(EGameplayModOp::Division), Parameters);
	...
	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
	...
}
```

```c++
float FAggregatorModChannel::SumMods(const TArray<FAggregatorMod>& InMods, float Bias, const FAggregatorEvaluateParameters& Parameters)
{
	float Sum = Bias;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Sum += (Mod.EvaluatedMagnitude - Bias);
		}
	}

	return Sum;
}
```

*摘自GameplayEffectAggregator.cpp*  

在该公式中`Multiply`和`Divide`Modifier都有一个值为1的`Bias`值(加法的`Bias`值为0), 因此它看起来像:  

```c++
1 + (Mod1.Magnitude - 1) + (Mod2.Magnitude - 1) + ...
```

该公式会导致一些意料之外的结果, 首先, 它在对BaseValue乘除之前将所有的`Modifier`都加到了一起, 大部分人都期望将其乘或除在一起, 例如, 你有两个值为1.5的`Multiply`Modifier, 大部分人都期望将BaseValue乘上`1.5 x 1.5 = 2.25`, 然而, 这里是将两个1.5加在一起再乘以BaseValue(50%增量 + 另一个50%增量 = 100%增量).拿`GameplayPrediction.h`中的一个例子来说, 给基值速度500加上10%的加速buff就是550, 再加上另一个10%的加速buff就是600.  

其次, 该公式还有一些对于可以使用哪些值而未说明的的规则, 因为这是考虑Paragon的情况而设计的.  

*译者注: 说实话, 我没有搞懂下文中原文档作者的逻辑, 可能是没有充分了解项目的原因? 比如在样例项目中, 删除BP_DamageVolume的GameplayEffect中的Executions, 并按照下文例4添加两个Multiply Multipliers, Attribute均为GDAttributeSetBase.XP, Modifier Magnitude均为Scalable Float/5.0, 回到游戏, 击杀一个小兵使XP增加到1, 然后进入BP_DamageVolume, 会发现XP依次变为25, 625..., 进行调试也会发现是Modifier依次相乘的, 并不是作者所说的乘法分配律逻辑. 还有就是为什么符合公式规则的`1 + (0.5 - 1) + (1.1 - 1) = 0.6`是正确的而不符合公式规则的`1 + (0.5 - 1) + (0.5 - 1) = 0`和`1 + (5 - 1) + (5 - 1) = 9`就是错误预期? 这个正确和错误预期是以什么为评判标准的? 是否符合公式规则么? 如果各位明白其中道理, 还请不吝赐教, 在此感谢!*  

对于`Multiply`和`Divide`中乘法加法公式的规则:  

* (最多不超过1个值 < 1) AND (任意数量值位于[1, 2))
* OR (一个值 >= 2)

公式中的Bias基本上都会减去`[1, 2)`区间中的整数位, 第一个Modifier的`Bias`会从最开始的`Sum`值减值(在循环体前设置Bias), 这就是为什么某个值它本身能起作用的原因以及某个小于1的值与`[1, 2)`区间中的值起作用的原因.  

`Multiply`的一些例子:  
Multipliers: 0.5  
`1 + (0.5 - 1) = 0.5`, 正确.  

Multipliers: 0.5, 0.5  
`1 + (0.5 - 1) + (0.5 - 1) = 0`, 错误预期`1`? 多个小于1的值在`Modifier`相加中不起作用. Paragon这样设计只是为了使用[Multiply Modifier的最负值](#cae-nonstackingge), 因此最多只会有一个小于1的值乘到BaseValue.  

Multipliers: 1.1, 0.5  
`1 + (0.5 - 1) + (1.1 - 1) = 0.6`, 正确.  

Multipliers: 5, 5
`1 + (5 - 1) + (5 - 1) = 9`, 错误预期`10`. 它总会是`Modifier值的和 - Modifier个数 + 1`.  

很多游戏会想要它们的`Modify`和`Divide`Modifier在应用到BaseValue之前先乘或除到一起, 为了实现这种需求, 你需要修改`FAggregatorModChannel::EvaluateWithBase()`的引擎代码.  

```c++
float FAggregatorModChannel::EvaluateWithBase(float InlineBaseValue, const FAggregatorEvaluateParameters& Parameters) const
{
	...
	float Multiplicitive = MultiplyMods(Mods[EGameplayModOp::Multiplicitive], Parameters);
	...

	return ((InlineBaseValue + Additive) * Multiplicitive) / Division;
}
```

```c++
float FAggregatorModChannel::MultiplyMods(const TArray<FAggregatorMod>& InMods, const FAggregatorEvaluateParameters& Parameters)
{
	float Multiplier = 1.0f;

	for (const FAggregatorMod& Mod : InMods)
	{
		if (Mod.Qualifies())
		{
			Multiplier *= Mod.EvaluatedMagnitude;
		}
	}

	return Multiplier;
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-mods-gameplaytags"></a>
##### 4.5.4.2 Modifier的GameplayTag

每个[Modifier](#concepts-ge-mods)都可以设置`SourceTag`和`TargetTag`, 它们的作用就像`GameplayEffect`的[Application Tag requirements](#concepts-ge-tags), 因此只有当Effect应用后才会考虑标签, 对于周期性(Periodic)的`无限(Infinite)`Effect, 这些标签只会在第一次应用Effect时才会被考虑, 而不是在每次周期执行时.  

`Attribute Based Modifier`也可以设置`SourceTagFilter`和`TargetTagFilter`. 当确定`Attribute Based Modifier`的源(Source)`Attribute`的Magnitude时, 这些过滤器就会用来将某些`Modifier`排除在该属性之外, 源(Source)或目标(Target)中没有过滤器所有标签的`Modifier`也会被排除在外.  

这更详尽的意思是: 源(Source)`ASC`和目标(Target)`ASC`的标签都被`GameplayEffect`所捕获, 当`GameplayEffectSpec`创建时, 源(Source)`ASC`的标签被捕获, 当执行Effect时, 目标(Target)`ASC`的标签被捕获. 当确定`无限(Infinite)`或`持续(Duration)`Effect的`Modifier`是否满足条件可以被应用(也就是聚合器条件(Aggregator Qualify))并且过滤器已经设置时, 被捕获的标签就会和过滤器进行比对.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-stacking"></a>
#### 4.5.5 GameplayEffect堆栈

`GameplayEffect`默认会应用新的`GameplayEffectSpec`实例, 而不明确或不关心之前已经应用过的尚且存在的`GameplayEffectSpec`实例. `GameplayEffect`可以设置到堆栈中, 新的`GameplayEffectSpec`实例不会添加到堆栈中, 而是修改当前已经存在的`GameplayEffectSpec`堆栈数. 堆栈只适用于`持续(Duration)`和`无限(Infinite)GameplayEffect`.  

有两种类型的堆栈: Aggregate by Source和Aggregate by Target.  

|堆栈类型|描述|
|:-:|:-:|
|Aggregate by Source|目标(Target)上的每个源(Source)`ASC`都有一个单独的堆栈实例, 每个源(Source)可以应用堆栈中的X个`GameplayEffect`.|
|Aggregate by Target|目标(Target)上只有一个堆栈实例而不管源(Source)如何, 每个源(Source)都可以在共享堆栈限制(Shared Stack Limit)内应用堆栈.|

堆栈对过期, 持续刷新和周期性刷新也有一些处理策略, 这些在`GameplayEffect`蓝图中都有很友好的悬浮提示帮助.  

样例项目包含一个用于监听`GameplayEffect`堆栈变化的自定义蓝图节点, HUD UMG Widget使用它来更新玩家拥有的被动护盾堆栈(层数). 该`AsyncTask`将会一直响应直到手动调用`EndTask()`, 就像在UMG Widget的`Destruct`事件中调用那样. 参阅`AsyncTaskAttributeChanged.h/cpp`.  

![Listen for GameplayEffect Stack Change BP Node](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gestackchange.png)

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ga"></a>
#### 4.5.6 授予Ability

`GameplayEffect`可以授予(Grant)新的[GameplayAbility](#concepts-ga)到`ASC`. 只有`持续(Duration)`和`无限(Infinite)GameplayEffect`可以授予Ability.  

一个普遍用法是当想要强制另一个玩家做某些事的时候, 像击退或拉取时移动他们, 就会对它们应用一个`GameplayEffect`来授予其一个自动激活的Ability(查看[被动Ability](#concepts-ga-activating-passive)来了解如何在Ability被授予时自动激活它), 从而使其做出相应的动作.  

设计师可以决定一个`GameplayEffect`能够授予哪些Ability, 授予的Ability等级, 将其[绑定](#concepts-ga-input)在什么输入键上以及该Ability的移除策略.  

|移除策略|描述|
|:-:|:-:|
|立即取消Ability|当授予Ability的`GameplayEffect`从目标移除时, 授予的Ability就会立即取消并移除.|
|结束时移除Ability|允许授予的Ability完成, 之后将其从目标移除.|
|无|授予的Ability不受从目标移除的授予`GameplayEffect`的影响, 目标将会一直拥有该Ability直到之后被手动移除.|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-tags"></a>
#### 4.5.7 GameplayEffect标签

`GameplayEffect`可以带有多个[GameplayTagContainer](#concepts-gt), 设计师可以编辑每个类别的`Added`和`Removed`GameplayTagContainer, 结果会在编译后显示在`Combined GameplayTagContainer`中. `Added`标签是该`GameplayEffect`新增的之前其父类没有的标签, `Removed`标签是其父类拥有但该类没有的标签.  

|分类|描述|
|:-:|:-:|
|Gameplay Effect Asset Tags|`GameplayEffect`拥有的标签, 它们自身没有任何功能且只用于描述`GameplayEffect`.|
|Granted Tags|存于`GameplayEffect`中且又用于`GameplayEffect`所应用`ASC`的标签. 当`GameplayEffect`移除时它们也会从`ASC`中移除. 该标签只作用于`持续(Duration)`和`无限(Infinite)GameplayEffect`.|
|Ongoing Tag Requirements|一旦`GameplayEffect`应用后, 这些标签将决定`GameplayEffect`是开启还是关闭. `GameplayEffect`可以是关闭但仍然是应用的. 如果某个`GameplayEffect`由于不符合`Ongoing Tag Requirements`而关闭, 但是之后又满足需求了, 那么该`GameplayEffect`会重新打开并重新应用它的`Modifier`. 该标签只作用于`持续(Duration)`和`无限(Infinite)GameplayEffect`.|
|Application Tag Requirements|位于目标上决定某个`GameplayEffect`是否可以应用到该目标的标签, 如果不满足这些需求, 那么`GameplayEffect`就不可应用.|
|Remove Gameplay Effects with Tags|当`GameplayEffect`成功应用后, 如果位于目标上的该`GameplayEffect`在其`Asset Tags`或`Granted Tags`中有任意一个本标签的话, 其就会自目标上移除.|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-immunity"></a>
#### 4.5.8 免疫

`GameplayEffect`可以基于[GameplayTag](#concepts-gt)实现免疫, 有效阻止其他`GameplayEffect`应用. 尽管免疫可以由`Application Tag Requirements`等方式有效地实现, 但是使用该系统可以在`GameplayEffect`被免疫阻止时提供`UAbilitySystemComponent::OnImmunityBlockGameplayEffectDelegate`委托(Delegate).  

`GrantedApplicationImmunityTags`会检查源(Source)`ASC`(包括源Ability的AbilityTag, 如果有的话)是否包含特定的标签, 这是一种基于确定Character或源(Source)的标签对其所有`GameplayEffect`提供免疫的方法.  

`Granted Application Immunity Query`会检查传入的`GameplayEffectSpec`是否与其查询条件相匹配, 从而阻止或允许其应用.  

`GameplayEffect`蓝图中的查询条件都有友好的悬浮提示帮助.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-spec"></a>
#### 4.5.9 GameplayEffectSpec

[GameplayEffectSpec(GESpec)](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectSpec/index.html)可以看作是`GameplayEffect`的实例, 它保存了一个其所代表的`GameplayEffect`类引用, 创建时的等级和创建者, 它在应用之前可以在运行时(Runtime)自由的创建和修改, 不像`GameplayEffect`应该由设计师在运行前创建. 当应用`GameplayEffect`时, `GameplayEffectSpec`会自`GameplayEffect`创建并且会实际应用到目标(Target).  

`GameplayEffectSpec`是由`UAbilitySystemComponent::MakeOutgoingSpec()(BlueprintCallable)`自`GameplayEffect`创建的. `GameplayEffectSpec`不必立即应用. 通常是将`GameplayEffectSpec`传递给创建自Ability的投掷物, 该投掷物可以应用到它之后击中的目标. 当`GameplayEffectSpec`成功应用后, 就会返回一个名为`FActiveGameplayEffect`的新结构体.  

`GameplayEffectSpec`的重要内容:  

* 创建该`GameplayEffectSpec`的`GameplayEffect`类.
* 该`GameplayEffectSpec`的等级. 通常和创建`GameplayEffectSpec`的Ability的等级一样, 但是可以是不同的.
* `GameplayEffectSpec`的持续时间. 默认是`GameplayEffect`的持续时间, 但是可以是不同的.
* 对于周期性Effect中`GameplayEffectSpec`的周期, 默认是`GameplayEffect`的周期, 但是可以是不同的.
* 该`GameplayEffectSpec`的当前堆栈数. 堆栈限制取决于`GameplayEffect`.
* [GameplayEffectContextHandle](#concepts-ge-context)表明该`GameplayEffectSpec`由谁创建.
* `Attribute`在`GameplayEffectSpec`创建时由于Snapshot被捕获.
* 除了`GameplayEffect`授予的`GameplayTags`, `GameplayEffectSpec`还会授予目标(Target)`DynamicGrantedTags`.
* 除了`GameplayEffect`拥有的`AssetTags`, `GameplayEffectSpec`还会拥有`DynamicAssetTags`.
* `SetByCaller TMaps`.

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-spec-setbycaller"></a>
##### 4.5.9.1 SetByCaller

`SetByCaller`允许`GameplayEffectSpec`拥有和`GameplayTag`或`FName`相关联的浮点值, 它们存储在`GameplayEffectSpec`上其各自的`TMaps: TMap<FGameplayTag, float>`和`TMap<FName, float>`中, 可以作为`GameplayEffect`的`Modifier`或者传递浮点值的通用方法使用. 其普遍用法是经由`SetByCaller`传递某个Ability内部生成的数值数据到[GameplayEffectExecutionCalculations](#concepts-ge-ec)或[ModifierMagnitudeCalculations](#concepts-ge-mmc).  

|SetByCaller使用|说明|
|:-:|:-:|
|`Modifier`|必须提前在`GameplayEffect`类中定义. 只能使用`GameplayTag`形式. 如果在`GameplayEffect`类中定义而`GameplayEffectSpec`中没有相应的标签/浮点值对, 那么游戏在`GameplayEffectSpec`应用时会抛出运行时错误并返回0, 这对于`Divide`操作是个潜在问题, 参阅[Modifier](#concepts-ge-mods).|
|其他|无需提前定义. 读取`GameplayEffectSpec`中不存在的`SetByCaller`会返回一个由开发者定义的可带有警告信息的默认值.|

为了在蓝图中指定`SetByCaller`值, 请使用相应形式(`GameplayTag`或`FName`)的蓝图节点.  

![Assigning SetByCaller](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/setbycaller.png)

为了在蓝图中读取`SetByCaller`值, 需要在蓝图中创建自定义节点.  

为了在C++中指定`SetByCaller`值, 需要使用相应形式的函数(`GameplayTag`或`FName`).  

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FName DataName, float Magnitude);
```

```c++
void FGameplayEffectSpec::SetSetByCallerMagnitude(FGameplayTag DataTag, float Magnitude);
```

为了在C++中读取`SetByCaller`的值, 需要使用相应形式的函数(`GameplayTag`或`FName`).  

```c++
float GetSetByCallerMagnitude(FName DataName, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

```c++
float GetSetByCallerMagnitude(FGameplayTag DataTag, bool WarnIfNotFound = true, float DefaultIfNotFound = 0.f) const;
```

我建议使用`GameplayTag`形式而不是`FName`形式, 这可以避免蓝图中的拼写错误, 并且当`GameplayEffectSpec`同步时, `GameplayTag`比`FName`在网络传输中更有效率, 因为`TMap`也会同步.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-context"></a>
#### 4.5.10 GameplayEffectContext

[GameplayEffectContext](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/FGameplayEffectContext/index.html)结构体存有关于`GameplayEffectSpec`创建者(Instigator)和[TargetData](#concepts-targeting-data)的信息, 这也是一个很好的可继承结构体以在[ModifierMagnitudeCalculation](#concepts-ge-mmc)/[GameplayEffectExecutionCalculation](#concepts-ge-ec), [AttributeSet](#concepts-as)和[GameplayCue](#concepts-gc)之间传递任意数据.  

继承`GameplayEffectContext`:  

1. 继承`FGameplayEffectContext`.
2. 重写`FGameplayEffectContext::GetScriptStruct()`.
3. 重写`FGameplayEffectContext::Duplicate()`.
4. 如果新数据需要同步的话, 重写`FGameplayEffectContext::NetSerialize()`.
5. 对子结构体实现`TStructOpsTypeTraits`, 就像父结构体`FGameplayEffectContext`有的那样.
6. 在[AbilitySystemGlobals](#concepts-asg)类中重写`AllocGameplayEffectContext()`以返回一个新的子结构体对象.

GASShooter使用了一个子结构体`GameplayEffectContext`来添加可以在`GameplayCue`中访问的`TargetData`, 特别是对于霰弹枪, 因为它可以击打多个敌人.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-mmc"></a>
#### 4.5.11 Modifier Magnitude Calculation

[ModifierMagnitudeCalculations](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayModMagnitudeCalculation/index.html)(`ModMagcCalc`或`MMC`)是在`GameplayEffect`中作为[Modifier](#concepts-ge-mods)使用的强有力的类, 它的功能类似[GameplayEffectExecutionCalculation](#concepts-ge-ec)但是要逊色一些, 最重要的是它是可预测的. 它唯一要做的就是自`CalculateBaseMagnitude_Implementation()`返回浮点值, 你可以在C++和蓝图中继承并重写该函数.  

`MMC`可以用于各种持续时间的`GameplayEffect` - `即刻(Instant)`, `持续(Duration)`, `无限(Infinite)`和`周期性(Periodic)`.  

`MMC`的优势在于能够完全访问`GameplayEffectSpec`来读取`GameplayTag`和`SetByCaller`，从而能够捕获`GameplayEffect`的`源(Source)`或`目标(Target)`上任意数量的`Attribute`值. `Attribute`可以被Snapshot也可以不被Snapshot, `Snapshotted Attribute`在`GameplayEffectSpec`创建时被捕获而非`Snapshotted Attribute`在`GameplayEffectSpec`应用时被捕获并且该`Attribute`被`无限(Infinite)`或`持续(Duration)`GameplayEffect修改时会自动更新. 捕获`Attribute`会自`ASC`现有的`Modifier`重新计算它们的`CurrentValue`, 该重新计算**不会**执行`AbilitySet`中的[PreAttributeChange()](#concepts-as-preattributechange), 因此所有的限制操作(Clamp)必须在这里重新处理.  

|Snapshot|源(Source)或目标(Target)|在GameplayEffectSpec中捕获|Attribute被无限(Infinite)或持续(Duration)GameplayEffect修改时自动更新|
|:-:|:-:|:-:|:-:|
|是|Source|创建|否|
|是|Target|应用|否|
|否|Source|应用|是|
|否|Target|应用|是|

`MMC`的结果浮点值可以进一步由系数和前后系数之和在`GameplayEffect`的`Modifier`中修改.  

举一个`MMC`的例子, 该`MMC`会捕获目标的魔法值`Attribute`并因为毒药Effect而将其减少, 其减少量的变化取决于目标所拥有的魔法值和可能拥有的某个标签:  

```c++
UPAMMC_PoisonMana::UPAMMC_PoisonMana()
{

	//ManaDef defined in header FGameplayEffectAttributeCaptureDefinition ManaDef;
	ManaDef.AttributeToCapture = UPAAttributeSetBase::GetManaAttribute();
	ManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	ManaDef.bSnapshot = false;

	//MaxManaDef defined in header FGameplayEffectAttributeCaptureDefinition MaxManaDef;
	MaxManaDef.AttributeToCapture = UPAAttributeSetBase::GetMaxManaAttribute();
	MaxManaDef.AttributeSource = EGameplayEffectAttributeCaptureSource::Target;
	MaxManaDef.bSnapshot = false;

	RelevantAttributesToCapture.Add(ManaDef);
	RelevantAttributesToCapture.Add(MaxManaDef);
}

float UPAMMC_PoisonMana::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	// Gather the tags from the source and target as that can affect which buffs should be used
	const FGameplayTagContainer* SourceTags = Spec.CapturedSourceTags.GetAggregatedTags();
	const FGameplayTagContainer* TargetTags = Spec.CapturedTargetTags.GetAggregatedTags();

	FAggregatorEvaluateParameters EvaluationParameters;
	EvaluationParameters.SourceTags = SourceTags;
	EvaluationParameters.TargetTags = TargetTags;

	float Mana = 0.f;
	GetCapturedAttributeMagnitude(ManaDef, Spec, EvaluationParameters, Mana);
	Mana = FMath::Max<float>(Mana, 0.0f);

	float MaxMana = 0.f;
	GetCapturedAttributeMagnitude(MaxManaDef, Spec, EvaluationParameters, MaxMana);
	MaxMana = FMath::Max<float>(MaxMana, 1.0f); // Avoid divide by zero

	float Reduction = -20.0f;
	if (Mana / MaxMana > 0.5f)
	{
		// Double the effect if the target has more than half their mana
		Reduction *= 2;
	}
	
	if (TargetTags->HasTagExact(FGameplayTag::RequestGameplayTag(FName("Status.WeakToPoisonMana"))))
	{
		// Double the effect if the target is weak to PoisonMana
		Reduction *= 2;
	}
	
	return Reduction;
}
```

如果你没有在`MMC`的构造函数中将`FGameplayEffectAttributeCaptureDefinition`添加到`RelevantAttributesToCapture`中并且尝试捕获`Attribute`, 那么将会得到一个关于捕获时缺失Spec的错误. 如果不需要捕获`Attribute`, 那么就不必添加什么到`RelevantAttributesToCapture`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ec"></a>
#### 4.5.12 Gameplay Effect Execution Calculation

[GameplayEffectExecutionCalculation](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectExecutionCalculat-/index.html)(ExecutionCalculation, Execution(你会在插件代码里经常看到这个词)或ExecCalc)是`GameplayEffect`对`ASC`进行修改最强有力的方式. 像[ModifierMagnitudeCalculation](#concepts-ge-mmc)一样, 它也可以捕获`Attribute`并选择性地为其创建Snapshot, 和`MMC`不同的是, 它可以修改多个`Attribute`并且基本上可以处理程序员想要做的任何事. 这种强有力和灵活性的负面就是它是不可[预测](#concepts-p)的且必须在C++中实现.  

`ExecutionCalculation`只能由`即刻(Instant)`和`周期性(Periodic)`GameplayEffect使用, 插件中所有和"Execute"相关的一般都引用到这两种类型的`GameplayEffect`.  

当`GameplayEffectSpec`创建时, Snapshot会捕获`Attribute`, 而当`GameplayEffectSpec`应用时, 非Snapshot会捕获`Attribute`. 捕获`Attribute`会自`ASC`现有的`Modifier`重新计算它们的`CurrentValue`, 该重新计算**不会**执行`AbilitySet`中的[PreAttributeChange()](#concepts-as-preattributechange), 因此所有的限制操作(Clamp)必须在这里重新处理.  

|快照|Source或Target|在GameplayEffectSpec中捕获|
|:-:|:-:|:-:|
|是|Source|创建|
|是|Target|应用|
|否|Source|应用|
|否|Target|应用|

为了设置`Attribute`捕获, 我们采用Epic的ActionRPG样例项目使用的方式, 定义一个保存和声明如何捕获`Attribute`的结构体, 并在该结构体的构造函数中创建一个它的副本(Copy). 每个`ExecCalc`都需要有一个这样的结构体. **Note**: 每个结构体需要一个独一无二的名字, 因为它们共享同一个命名空间, 多个结构体使用相同名字在捕获`Attribute`时会造成错误(大多是捕获到错误的`Attribute`值).  

对于`Local Predicted`, `Server Only`和`Server Initiated`的[GameplayAbility](#concepts-ga), `ExecCalc`只在服务端调用.  

`ExecCalc`最普遍的应用场景是计算一个来自很多`源(Source)`和`目标(Target)`中`Attribute`伤害值的复杂公式. 样例项目中有一个简单的`ExecCalc`用于计算伤害值, 其从`GameplayEffectSpec`的[SetByCaller](#concepts-ge-spec-setbycaller)中读取伤害值, 之后基于从`目标(Target)`捕获的护盾`Attribute`来减少该伤害值. 参阅`GDDamageExecCalculation.cpp/.h`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ec-senddata"></a>
##### 4.5.12.1 发送数据到Execution Calculation

除了捕获`Attribute`, 还有几种方法可以发送数据到`ExecutionCalculation`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ec-senddata-setbycaller"></a>
###### 4.5.12.1.1 SetByCaller

任何[设置在`GameplayEffectSpec`中的`SetByCaller`](#concepts-ge-spec-setbycaller)都可以直接在`ExecutionCalculation`中读取.  

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
float Damage = FMath::Max<float>(Spec.GetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName("Data.Damage")), false, -1.0f), 0.0f);
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ec-senddata-backingdataattribute"></a>
###### 4.5.12.1.2 支持(Backing)数据Attribute计算Modifier

如果你想硬编码值到`GameplayEffect`, 可以使用`CalculationModifier`传递, 其使用捕获的`Attribute`之一作为支持(Backing)数据.  

在这个截图例子中, 我们给捕获的伤害值`Attribute`增加了50, 你也可以将其设为`Override`来直接传入硬编码值.  

![Backing Data Attribute Calculation Modifier](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/calculationmodifierbackingdataattribute.png)

当`ExecutionCalculation`捕获该`Attribute`时会读取这个值.  

```c++
float Damage = 0.0f;
// Capture optional damage value set on the damage GE as a CalculationModifier under the ExecutionCalculation
ExecutionParams.AttemptCalculateCapturedAttributeMagnitude(DamageStatics().DamageDef, EvaluationParameters, Damage);
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ec-senddata-backingdatatempvariable"></a>
###### 4.5.12.1.3 支持(Backing)数据临时变量计算Modifier

如果你想硬编码值到`GameplayEffect`, 可以在C++中使用`CalculationModifier`传递, 其使用一个`临时变量`或`暂时聚合器(Transient Aggregator)`, 该`临时变量`与`GameplayTag`相关联.  

在这个截图例子中, 我们使用`Data.Damage GameplayTag`增加50到一个临时变量.  

![Backing Data Temporary Variable Calculation Modifier](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/calculationmodifierbackingdatatempvariable.png)

添加支持(Backing)临时变量到你的`ExecutionCalculation`构造函数:  

```c++
ValidTransientAggregatorIdentifiers.AddTag(FGameplayTag::RequestGameplayTag("Data.Damage"));
```

`ExecutionCalculation`会使用和`Attribute`捕获函数相似的特殊捕获函数来读取这个值.  

```c++
float Damage = 0.0f;
ExecutionParams.AttemptCalculateTransientAggregatorMagnitude(FGameplayTag::RequestGameplayTag("Data.Damage"), EvaluationParameters, Damage);
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-ec-senddata-effectcontext"></a>
###### 4.5.12.1.4 GameplayEffectContext

你可以通过[`GameplayEffectSpec`中的自定义`GameplayEffectContext`](#concepts-ge-context)发送数据到`ExecutionCalculation`.  

在`ExecutionCalculation`中, 你可以自`FGameplayEffectCustomExecutionParameters`访问`EffectContext`.  

```c++
const FGameplayEffectSpec& Spec = ExecutionParams.GetOwningSpec();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(Spec.GetContext().Get());
```

如果你需要修改`GameplayEffectSpec`中的什么或者`EffectContext`:  

```c++
FGameplayEffectSpec* MutableSpec = ExecutionParams.GetOwningSpecForPreExecuteMod();
FGSGameplayEffectContext* ContextHandle = static_cast<FGSGameplayEffectContext*>(MutableSpec->GetContext().Get());
```

在`ExecutionCalculation`中修改`GameplayEffectSpec`时要小心. 参看`GetOwningSpecForPreExecuteMod()`的注释.  

```c++
/** Non const access. Be careful with this, especially when modifying a spec after attribute capture. */
FGameplayEffectSpec* GetOwningSpecForPreExecuteMod() const;
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-car"></a>
#### 4.5.13 自定义应用需求

[CustomApplicationRequirement(CAR)](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayEffectCustomApplication-/index.html)类为设计师提供对于`GameplayEffect`是否可以应用的高阶控制, 而不是对`GameplayEffect`进行简单的`GameplayTag`检查. 这可以通过在蓝图中重写`CanApplyGameplayEffect()`和在C++中重写`CanApplyGameplayEffect_Implementation()`实现.  

`CAR`的应用场景:  

* 目标需要有一定数量的`Attribute`.
* 目标需要有一定数量的`GameplayEffect`堆栈.

`CAR`还有很多高阶功能, 像检查`GameplayEffect`实例是否已经位于目标上, 修改当前实例的[持续时间](#concepts-ge-duration)而不是应用一个新实例(对于`CanApplyGameplayEffect()`返回false).

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-cost"></a>
#### 4.5.14 花费(Cost)GameplayEffect

[GameplayAbility](#concepts-ga)有一个特别设计用来作为Ability花费(Cost)的可选`GameplayEffect`. 花费(Cost)是指`ASC`激活`GameplayAbility`所必需的`Attribute`量. 如果某个`GA`不能提供`Cost GE`, 那么它就不能被激活. 该`Cost GE`应该是某个带有一个或多个自`Attribute`中减值Modifier的`即刻(Instant)GameplayEffect`. 默认情况下, `Cost GE`是可以被预测的, 建议保留该功能, 也就是不要使用`ExecutionCalculations`, `MMC`对于复杂的花费计算是完美适配并且鼓励使用的.  

开始的时候, 你通常会为每个有花费的`GA`都设置一个独一无二的`Cost GE`, 一个更高阶的技巧是对多个`GA`复用一个`Cost GE`, 只需修改自`GA`的`Cost GE`创建的`GameplayEffectSpec`中指定的数据(花费值是在`GA`上定义的), **这只作用于`实例化(Instanced)`的Ability.**  

复用`Cost GE`的两种技巧:  

1. 使用`MMC`. 这是最简单的方式. 创建一个从`GameplayAbility`实例读取花费值的[MMC](#concepts-ge-mmc), 你可以从`GameplayEffectSpec`中获取到该实例.  

```c++
float UPGMMC_HeroAbilityCost::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->Cost.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

在这个例子中, 花费值是一个我添加到`GameplayAbility`子类上的`FScalableFloat`.  

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cost")
FScalableFloat Cost;
```

![Cost GE With MMC](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/costmmc.png)

2. **重写`UGameplayAbility::GetCostGameplayEffect()`.** 重写该函数并在[运行时](#concepts-ge-dynamic)创建一个用来读取`GameplayAbility`中花费值的`GameplayEffect`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-cooldown"></a>
#### 4.5.15 冷却(Cooldown)GameplayEffect

[GameplayAbility](#concepts-ga)有一个特别设计用来作为Ability冷却(Cooldown)的可选`GameplayEffect`. 冷却时间决定了激活Ability之后多久可以再次激活. 如果某个`GA`在冷却中, 那么它就不能被激活. 该`Cooldown GE`应该是一个不带有`Modifier`的`持续(Duration)GameplayEffect`, 并且在`GameplayEffect`的`GrantedTags`中每个`GameplayAbility`或Ability插槽(Slot)(如果你的游戏有分配到插槽的可交换Ability且共享同一个冷却)都有一个独一无二的`GameplayTag`(`Cooldown Tag`). `GA`实际上会检查`Cooldown Tag`的存在而不是`Cooldown GE`的存在, 默认情况下, `Cooldown GE`是可以被预测的, 建议保留该功能, 也就是不要使用`ExecutionCalculations`, `MMC`对于复杂的冷却计算是完美适配并且鼓励使用的.  

开始的时候, 你通常会为每个有冷却的`GA`都设置一个独一无二的`Cooldown GE`, 一个更高阶的技巧是对多个`GA`复用一个`Cooldown GE`, 只需修改自`GA`的`Cooldown GE`创建的`GameplayEffectSpec`中指定的数据(冷却时间和`Cooldown Tag`是在`GA`上定义的), **这只作用于`实例化(Instanced)`的Ability.**  

复用`Cooldown GE`的两种技巧:  

1. 使用[SetByCaller](#concepts-ge-spec-setbycaller). 这是最简单的方式. 使用`GameplayTag`设置`SetByCaller`为共享`Cooldown GE`的持续时间. 在`GameplayAbility`子类中, 为持续时间定义一个浮点/`FScalableFloat`变量, 为独一无二的`Cooldown Tag`定义一个`FGameplayTagContainer`, 除此之外还要定义一个临时`FGameplayTagContainer`, 其用来作为`Cooldown Tag`与`Cooldown GE`标签并集的返回指针.  

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```

之后重写`UGameplayAbility::GetCooldownTags()`以返回`Cooldown Tag`和所有现有`Cooldown GE`标签的并集.  

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后, 重写`UGameplayAbility::ApplyCooldown()`以注入我们自己的`Cooldown Tag`, 并将`SetByCaller`添加到`Cooldown GameplayEffectSpec`.  

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		SpecHandle.Data.Get()->SetSetByCallerMagnitude(FGameplayTag::RequestGameplayTag(FName(  OurSetByCallerTag  )), CooldownDuration.GetValueAtLevel(GetAbilityLevel()));
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

下面图片中, 冷却时间`Modifier`被设置为`SetByCaller`, 其`Data Tag`为`Data.Cooldown`. `Data.Cooldown`就是上面代码中的`OurSetByCallerTag`.  

![Cooldown GE with SetByCaller](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/cooldownsbc.png)  

2. 使用[MMC](#concepts-ge-mmc). 它的设置与上文所提的一致, 除了不需要在`Cooldown GE`和`ApplyCost`中设置`SetByCaller`作为持续时间, 相反, 我们需要将持续时间设置为`Custom Calculation类`并将其指向新创建的`MMC`.  

```c++
UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FScalableFloat CooldownDuration;

UPROPERTY(BlueprintReadOnly, EditAnywhere, Category = "Cooldown")
FGameplayTagContainer CooldownTags;

// Temp container that we will return the pointer to in GetCooldownTags().
// This will be a union of our CooldownTags and the Cooldown GE's cooldown tags.
UPROPERTY()
FGameplayTagContainer TempCooldownTags;
```

之后重写`UGameplayAbility::GetCooldownTags()`以返回`Cooldown Tag`和所有现有`Cooldown GE`标签的并集.  

```c++
const FGameplayTagContainer * UPGGameplayAbility::GetCooldownTags() const
{
	FGameplayTagContainer* MutableTags = const_cast<FGameplayTagContainer*>(&TempCooldownTags);
	const FGameplayTagContainer* ParentTags = Super::GetCooldownTags();
	if (ParentTags)
	{
		MutableTags->AppendTags(*ParentTags);
	}
	MutableTags->AppendTags(CooldownTags);
	return MutableTags;
}
```

最后, 重写`UGameplayAbility::ApplyCooldown()`以将我们的`Cooldown Tag`注入`Cooldown GameplayEffectSpec`.  

```c++
void UPGGameplayAbility::ApplyCooldown(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo) const
{
	UGameplayEffect* CooldownGE = GetCooldownGameplayEffect();
	if (CooldownGE)
	{
		FGameplayEffectSpecHandle SpecHandle = MakeOutgoingGameplayEffectSpec(CooldownGE->GetClass(), GetAbilityLevel());
		SpecHandle.Data.Get()->DynamicGrantedTags.AppendTags(CooldownTags);
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, SpecHandle);
	}
}
```

```c++
float UPGMMC_HeroAbilityCooldown::CalculateBaseMagnitude_Implementation(const FGameplayEffectSpec & Spec) const
{
	const UPGGameplayAbility* Ability = Cast<UPGGameplayAbility>(Spec.GetContext().GetAbilityInstance_NotReplicated());

	if (!Ability)
	{
		return 0.0f;
	}

	return Ability->CooldownDuration.GetValueAtLevel(Ability->GetAbilityLevel());
}
```

![Cooldown GE with MMC](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/cooldownmmc.png)  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-cooldown-tr"></a>
##### 4.5.15.1 获取Cooldown GameplayEffect的剩余时间

```c++
bool APGPlayerState::GetCooldownRemainingForTag(FGameplayTagContainer CooldownTags, float & TimeRemaining, float & CooldownDuration)
{
	if (AbilitySystemComponent && CooldownTags.Num() > 0)
	{
		TimeRemaining = 0.f;
		CooldownDuration = 0.f;

		FGameplayEffectQuery const Query = FGameplayEffectQuery::MakeQuery_MatchAnyOwningTags(CooldownTags);
		TArray< TPair<float, float> > DurationAndTimeRemaining = AbilitySystemComponent->GetActiveEffectsTimeRemainingAndDuration(Query);
		if (DurationAndTimeRemaining.Num() > 0)
		{
			int32 BestIdx = 0;
			float LongestTime = DurationAndTimeRemaining[0].Key;
			for (int32 Idx = 1; Idx < DurationAndTimeRemaining.Num(); ++Idx)
			{
				if (DurationAndTimeRemaining[Idx].Key > LongestTime)
				{
					LongestTime = DurationAndTimeRemaining[Idx].Key;
					BestIdx = Idx;
				}
			}

			TimeRemaining = DurationAndTimeRemaining[BestIdx].Key;
			CooldownDuration = DurationAndTimeRemaining[BestIdx].Value;

			return true;
		}
	}

	return false;
}
```

**Note:** 在客户端上查询剩余冷却时间要求其可以接收同步的`GameplayEffect`, 这依赖于它们`ASC`的[同步模式](#concepts-asc-rm).  

**[⬆ 返回目录](#table-of-contents)**

 <a name="concepts-ge-cooldown-listen"></a>
##### 4.5.15.2 监听冷却开始和结束

为了监听某个冷却何时开始, 你可以通过绑定`AbilitySystemComponent->OnActiveGameplayEffectAddedDelegateToSelf`或者`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`分别在`Cooldown GE`应用和`Cooldown Tag`添加时作出响应. 我建议监听`Cooldown GE`何时应用, 因为这时还可以访问应用它的`GameplayEffectSpec`. 由此你可以确定当前`Cooldown GE`是客户端预测的还是由服务端校正的.  

为了监听某个冷却何时结束, 你可以通过绑定`AbilitySystemComponent->OnAnyGameplayEffectRemovedDelegate()`或者`AbilitySystemComponent->RegisterGameplayTagEvent(CooldownTag, EGameplayTagEventType::NewOrRemoved)`分别在`Cooldown GE`移除和`Cooldown Tag`移除时作出响应. 我建议监听`Cooldown Tag`何时移除, 因为当服务端校正的`Cooldown GE`到来时, 会移除客户端预测的`Cooldown GE`, 这会响应`OnAnyGameplayEffectRemovedDelegate()`, 即使仍处于冷却过程中. 预测的`Cooldown GE`在移除时和服务端校正的`Cooldown GE`在应用时`Cooldown Tag`都不会改变.  

**Note:** 在客户端上监听某个`GameplayEffect`添加或移除要求其可以接收同步的`GameplayEffect`, 这依赖于它们`ASC`的[同步模式](#concepts-asc-rm).  

样例项目包含一个用于监听冷却开始和结束的自定义蓝图节点, HUD UMG Widget使用它来更新陨石技能的剩余冷却时间, 该`AsyncTask`会一直响应直到手动调用`EndTask()`, 就像在UMG Widget的`Destruct`事件中调用那样. 参阅`AsyncTaskAttributeChanged.h/cpp`.  

![Listen for Cooldown Change BP Node](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/cooldownchange.png)  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-cooldown-prediction"></a>
##### 4.5.15.3 预测冷却时间

目前冷却时间不是真正可预测的. 我们可以在客户端预测的`Cooldown GE`应用时启动UI的冷却时间计时器, 但是`GameplayAbility`的实际冷却时间是由服务端的冷却时间剩余决定的. 取决于玩家的延迟情况, 可能客户端预测的冷却已经结束, 但是服务端上的`GameplayAbility`仍处于冷却过程, 这会阻止`GameplayAbility`的立刻再激活直到服务端冷却结束.  

样例项目通过在客户端预测的冷却开始时灰化陨石技能的图标, 之后在服务端校正的`Cooldown GE`到来时启动冷却计时器处理该问题.  

在实际游戏中导致的结果就是高延迟的玩家相比低延迟的玩家对冷却时间短的技能有更低的触发率, 从而处于劣势, Fortnite通过使其武器使用无需冷却`GameplayEffect`的自定义Bookkeeping而避免该现象.  

Epic希望在未来的[GAS迭代版本](#concepts-p-future)中实现真正的冷却预测(玩家可以激活一个在客户端冷却完成但服务端仍处于冷却过程的`GameplayAbility`).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-duration"></a>
#### 4.5.16 修改已激活GameplayEffect的持续时间

为了修改`Cooldown GE`或其他任何`持续(Duration)`GameplayEffect的剩余时间, 我们需要修改`GameplayEffectSpec`的持续时间, 更新它的`StartServerWorldTime`, `CachedStartServerWorldTime`, `StartWorldTime`, 并且使用`CheckDuration()`重新检查持续时间. 在服务端上完成这些操作并将`FActiveGameplayEffect`标记为dirty, 其会将这些修改同步到客户端. **Note:** 该操作包含一个`const_cast`, 这可能不是`Epic`希望的修改持续时间的方法, 但是迄今为止它看起来运行得很好.  

```c++
bool UPAAbilitySystemComponent::SetGameplayEffectDurationHandle(FActiveGameplayEffectHandle Handle, float NewDuration)
{
	if (!Handle.IsValid())
	{
		return false;
	}

	const FActiveGameplayEffect* ActiveGameplayEffect = GetActiveGameplayEffect(Handle);
	if (!ActiveGameplayEffect)
	{
		return false;
	}

	FActiveGameplayEffect* AGE = const_cast<FActiveGameplayEffect*>(ActiveGameplayEffect);
	if (NewDuration > 0)
	{
		AGE->Spec.Duration = NewDuration;
	}
	else
	{
		AGE->Spec.Duration = 0.01f;
	}

	AGE->StartServerWorldTime = ActiveGameplayEffects.GetServerWorldTime();
	AGE->CachedStartServerWorldTime = AGE->StartServerWorldTime;
	AGE->StartWorldTime = ActiveGameplayEffects.GetWorldTime();
	ActiveGameplayEffects.MarkItemDirty(*AGE);
	ActiveGameplayEffects.CheckDuration(Handle);

	AGE->EventSet.OnTimeChanged.Broadcast(AGE->Handle, AGE->StartWorldTime, AGE->GetDuration());
	OnGameplayEffectDurationChange(*AGE);

	return true;
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-dynamic"></a>
#### 4.5.17 运行时创建动态`GameplayEffect`

在运行时创建动态`GameplayEffect`是一个高阶技术, 你不必经常使用它.  

只有`即刻(Instant)GameplayEffect`可以在运行时由C++创建, `持续(Duration)`和`无限(Infinite)`GameplayEffect不能在运行时动态创建, 因为它们在同步时会寻找并不存在的`GameplayEffect`类定义. 为了实现该功能, 你应该创建一个原型`GameplayEffect`类, 就像平时在编辑器中做的那样, 之后根据运行时所需来定制化`GameplayEffectSpec`.  

运行时创建的`即刻(Instant)GameplayEffect`也可以在客户端[预测](#concepts-p)的`GameplayAbility`中调用. 然而, 目前还不明确动态创建是否有副作用.  

样例项目会在角色`AttributeSet`中的值受到致命一击时创建该`GameplayEffect`来将金币和经验点数返还给击杀者.  

```c++
// Create a dynamic instant Gameplay Effect to give the bounties
UGameplayEffect* GEBounty = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Bounty")));
GEBounty->DurationPolicy = EGameplayEffectDurationType::Instant;

int32 Idx = GEBounty->Modifiers.Num();
GEBounty->Modifiers.SetNum(Idx + 2);

FGameplayModifierInfo& InfoXP = GEBounty->Modifiers[Idx];
InfoXP.ModifierMagnitude = FScalableFloat(GetXPBounty());
InfoXP.ModifierOp = EGameplayModOp::Additive;
InfoXP.Attribute = UGDAttributeSetBase::GetXPAttribute();

FGameplayModifierInfo& InfoGold = GEBounty->Modifiers[Idx + 1];
InfoGold.ModifierMagnitude = FScalableFloat(GetGoldBounty());
InfoGold.ModifierOp = EGameplayModOp::Additive;
InfoGold.Attribute = UGDAttributeSetBase::GetGoldAttribute();

Source->ApplyGameplayEffectToSelf(GEBounty, 1.0f, Source->MakeEffectContext());
```

第二个样例展示了在一个客户端预测的`GameplayAbility`中创建运行时`GameplayEffect`, 使用风险自负(查看代码中的注释)!

```c++
UGameplayAbilityRuntimeGE::UGameplayAbilityRuntimeGE()
{
	NetExecutionPolicy = EGameplayAbilityNetExecutionPolicy::LocalPredicted;
}

void UGameplayAbilityRuntimeGE::ActivateAbility(const FGameplayAbilitySpecHandle Handle, const FGameplayAbilityActorInfo* ActorInfo, const FGameplayAbilityActivationInfo ActivationInfo, const FGameplayEventData* TriggerEventData)
{
	if (HasAuthorityOrPredictionKey(ActorInfo, &ActivationInfo))
	{
		if (!CommitAbility(Handle, ActorInfo, ActivationInfo))
		{
			EndAbility(Handle, ActorInfo, ActivationInfo, true, true);
		}

		// Create the GE at runtime.
		UGameplayEffect* GameplayEffect = NewObject<UGameplayEffect>(GetTransientPackage(), TEXT("RuntimeInstantGE"));
		GameplayEffect->DurationPolicy = EGameplayEffectDurationType::Instant; // Only instant works with runtime GE.

		// Add a simple scalable float modifier, which overrides MyAttribute with 42.
		// In real world applications, consume information passed via TriggerEventData.
		const int32 Idx = GameplayEffect->Modifiers.Num();
		GameplayEffect->Modifiers.SetNum(Idx + 1);
		FGameplayModifierInfo& ModifierInfo = GameplayEffect->Modifiers[Idx];
		ModifierInfo.Attribute.SetUProperty(UMyAttributeSet::GetMyModifiedAttribute());
		ModifierInfo.ModifierMagnitude = FScalableFloat(42.f);
		ModifierInfo.ModifierOp = EGameplayModOp::Override;

		// Apply the GE.

		// Create the GESpec here to avoid the behavior of ASC to create GESpecs from the GE class default object.
		// Since we have a dynamic GE here, this would create a GESpec with the base GameplayEffect class, so we
		// would lose our modifiers. Attention: It is unknown, if this "hack" done here can have drawbacks!
		// The spec prevents the GE object being collected by the GarbageCollector, since the GE is a UPROPERTY on the spec.
		FGameplayEffectSpec* GESpec = new FGameplayEffectSpec(GameplayEffect, {}, 0.f); // "new", since lifetime is managed by a shared ptr within the handle
		ApplyGameplayEffectSpecToOwner(Handle, ActorInfo, ActivationInfo, FGameplayEffectSpecHandle(GESpec));
	}
	EndAbility(Handle, ActorInfo, ActivationInfo, false, false);
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ge-containers"></a>
#### 4.5.18 GameplayEffect Containers

Epic的[Action RPG](https://www.unrealengine.com/marketplace/en-US/slug/action-rpg)样例项目实现了一个名为`FGameplayEffectContainer`的结构体, 它不属于原生GAS, 但是对于包含`GameplayEffect`和[TargetData](#concepts-targeting-data)极其好用, 它会使一些过程自动化, 比如从`GameplayEffect`中创建`GameplayEffectSpec`并在其`GameplayEffectContext`中设置默认值. 在`GameplayAbility`中创建`GameplayEffectContainer`并将其传递给已生成的投掷物是非常简单和显而易见的, 然而我没有选择在样例项目中实现`GameplayEffectContainer`, 因为我想向你展示的是没有它的原生GAS, 但是我高度建议你研究一下它并将其纳入到你的项目中.  

为了访问`GameplayEffectContainer`中的`GESpec`以求做一些诸如添加`SetByCaller`的操作, 请使用`FGameplayEffectContainer`结构体中的`GESpec`数组索引访问`GESpec`引用, 这要求你需要提前知道想要访问的`GESpec`的索引.  

![SetByCaller with a GameplayEffectContainer](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gecontainersetbycaller.png)  

`GameplayEffectContainer`还包含一个可选的用于[定位(Target)](#concepts-targeting-containers)的高效方法.

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga"></a>
### 4.6 Gameplay Abilities

<a name="concepts-ga-definition"></a>
#### 4.6.1 GameplayAbility定义

[GameplayAbility(GA)](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/UGameplayAbility/index.html)是Actor在游戏中可以触发的一切行为和技能. 多个`GameplayAbility`可以在同一时刻激活, 例如奔跑和射击. 其可由蓝图或C++完成.  

`GameplayAbility`示例:  

* 跳跃
* 奔跑
* 射击
* 每X秒被动地阻挡一次攻击
* 使用药剂
* 开门
* 收集资源
* 建造

不应该使用`GameplayAbility`的场景:  

* 基础移动输入
* 一些与UI的交互 - 不要使用`GameplayAbility`从商店中购买物品

这些不是规定, 只是我的建议而已, 你的设计和实现可能是多样的.  

`GameplayAbility`自带有根据等级修改Attribute变化量或者`GameplayAbility`作用的默认功能.  

`GameplayAbility`运行在所属(Owning)客户端还是服务端取决于[网络执行策略(Net Execution Policy)](#concepts-ga-net)而不是Simulated Proxy. `网络执行策略(Net Execution Policy)`决定某个`GameplayAbility`是否是客户端可[预测](#concepts-p)的, 其对于[可选的Cost和`Cooldown GameplayEffect`](#concepts-ga-commit)包含有默认行为. `GameplayAbility`使用[AbilityTask](#concepts-at)用于随时间推移而发生的行为, 例如等待某个事件, 等待某个Attribute改变, 等待玩家选择一个目标或者使用`Root Motion Source`移动某个`Character`. **Simulated Client不会运行`GameplayAbility`,** 而是当服务端执行`Ability`时, 任何需要在Simulated Proxy上展现的视觉效果(像动画蒙太奇)将会被同步(Replicate)或者通过`AbilityTask`进行RPC或者对于像声音和粒子这样的装饰效果使用[GameplayCue](#concepts-gc).  

所有的`GameplayAbility`都会有它们各自由你的游戏逻辑重写的`ActivateAbility()`函数, 附加的逻辑可以添加到`EndAbility()`, 其会在`GameplayAbility`完成或取消时执行.  

一个简单的`GameplayAbility`流程图: ![Simple GameplayAbility Flowchart](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/abilityflowchartsimple.png)  

一个更复杂`GameplayAbility`流程图: ![Complex GameplayAbility Flowchart](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/abilityflowchartcomplex.png)  

复杂的Ability可以使用多个相互交互(激活, 取消等等)的`GameplayAbility`实现.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-definition-reppolicy"></a>
##### 4.6.1.1 Replication Policy

不要使用该选项. 这个名字会误导你并且你并不需要它. [GameplayAbilitySpec](#concepts-ga-spec)默认会从服务端向所属(Owning)客户端同步, 上文提到过, **`GameplayAbility`不会运行在Simulated Proxy上,** 其使用`AbilityTask`和`GameplayCue`来同步或者RPC视觉变化到Simulated Proxy. Epic的Dave Ratti已经表明要在未来[移除该选项](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)的意愿.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-definition-remotecancel"></a>
##### 4.6.1.2 Server Respects Remote Ability Cancellation

这个选项往往会引起麻烦. 它的意思是如果客户端的`GameplayAbility`由于玩家取消或者自然完成时, 就会强制它的服务端版本结束而不管其是否完成. 最重要的是之后的问题, 特别是对于高延迟玩家所使用的客户端预测的`GameplayAbility`. 一般情况下禁用该选项.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-definition-repinputdirectly"></a>
##### 4.6.1.3 Replicate Input Directly

设置该选项就会一直向服务端同步输入的按下(Press)和抬起(Release)事件. Epic不建议使用该选项而是依靠创建在已有输入相关的[AbilityTask](#concepts-at)中的`Generic Replicated Event`(如果你的[输入绑定在ASC](#concepts-ga-input)).  

Epic的注释:  

```c++
/** Direct Input state replication. These will be called if bReplicateInputDirectly is true on the ability and is generally not a good thing to use. (Instead, prefer to use Generic Replicated Events). */
UAbilitySystemComponent::ServerSetInputPressed()
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-input"></a>
#### 4.6.2 绑定输入到ASC

`ASC`允许你直接绑定输入事件并当你授予`GameplayAbility`时分配这些输入到`GameplayAbility`, 如果`GameplayTag`合乎要求, 当按下按键时, 分配到`GameplayAbility`的输入事件会自动激活各自的`GameplayAbility`. 分配的输入事件要求使用响应输入的内建`AbilityTask`.  

除了分配的输入事件可以激活`GameplayAbility`, `ASC`也接受一般的`Confirm`和`Cancel`输入, 这些特殊输入被`AbilityTask`用来确定像[Target Actor](#concepts-targeting-actors)的对象或取消它们.  

为了绑定输入到`ASC`, 你必须首先创建一个枚举来将输入事件名称转换为byte, 枚举名必须准确匹配项目设置中用于输入事件的名称, `DisplayName`就无所谓了.  

样例项目中:  

```c++
UENUM(BlueprintType)
enum class EGDAbilityInputID : uint8
{
	// 0 None
	None			UMETA(DisplayName = "None"),
	// 1 Confirm
	Confirm			UMETA(DisplayName = "Confirm"),
	// 2 Cancel
	Cancel			UMETA(DisplayName = "Cancel"),
	// 3 LMB
	Ability1		UMETA(DisplayName = "Ability1"),
	// 4 RMB
	Ability2		UMETA(DisplayName = "Ability2"),
	// 5 Q
	Ability3		UMETA(DisplayName = "Ability3"),
	// 6 E
	Ability4		UMETA(DisplayName = "Ability4"),
	// 7 R
	Ability5		UMETA(DisplayName = "Ability5"),
	// 8 Sprint
	Sprint			UMETA(DisplayName = "Sprint"),
	// 9 Jump
	Jump			UMETA(DisplayName = "Jump")
};
```

如果你的`ASC`位于`Character`, 那么就在`SetupPlayerInputComponent()`中包含用于绑定到`ASC`的函数.  

```c++
// Bind to AbilitySystemComponent
AbilitySystemComponent->BindAbilityActivationToInputComponent(PlayerInputComponent, FGameplayAbilityInputBinds(FString("ConfirmTarget"), FString("CancelTarget"), FString("EGDAbilityInputID"), static_cast<int32>(EGDAbilityInputID::Confirm), static_cast<int32>(EGDAbilityInputID::Cancel)));
```

如果你的`ASC`位于`PlayerState`, `SetupPlayerInputComponent()`中有一个潜在的竞争情况就是`PlayerState`还没有同步到客户端, 因此, 我建议尝试在`SetupPlayerInputComponent()`和`OnRep_PlayerState()`中绑定输入, 只有`OnRep_PlayerState()`自身是不充分的, 因为可能有种情况是当`PlayerState`在`PlayerController`告知客户端调用用于创建`InputComponent`的`ClientRestart()`前同步时, Actor的`InputComponent`可能为NULL. 样例项目演示了尝试使用一个布尔值控制流程从而在两个位置绑定, 这样实际上只绑定了一次.  

**Note:** 样例项目枚举中的`Confirm`和`Cancel`没有匹配项目设置中的输入事件名称(`ConfirmTarget`和`CancelTarget`), 但是我们在`BindAbilityActivationToInputComponent()`中提供了它们之间的映射, 这是特殊的, 因为我们提供了映射并且它们无需匹配, 但是它们是可以匹配的. 枚举中的其他输入都必须匹配项目设置中的输入事件名称.  

对于只能用一次输入激活的`GameplayAbility`(它们总是像MOBA游戏一样存在于相同的"槽"中), 我倾向在`UGameplayAbility`子类中添加一个变量, 这样我就可以定义他们的输入, 之后在授予Ability的时候可以从`ClassDefaultObject`中读取这个值.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-input-noactivate"></a>
##### 4.6.2.1 绑定输入时不激活Ability

如果你不想你的`GameplayAbility`在按键按下时自动激活, 但是仍想将它们绑定到输入以与`AbilityTask`一起使用, 你可以在`UGameplayAbility`子类中添加一个新的布尔变量, `bActivateOnInput`, 其默认值为`true`并重写`UAbilitySystemComponent::AbilityLocalInputPressed()`.  

```c++
void UGSAbilitySystemComponent::AbilityLocalInputPressed(int32 InputID)
{
	// Consume the input if this InputID is overloaded with GenericConfirm/Cancel and the GenericConfim/Cancel callback is bound
	if (IsGenericConfirmInputBound(InputID))
	{
		LocalInputConfirm();
		return;
	}

	if (IsGenericCancelInputBound(InputID))
	{
		LocalInputCancel();
		return;
	}

	// ---------------------------------------------------------

	ABILITYLIST_SCOPE_LOCK();
	for (FGameplayAbilitySpec& Spec : ActivatableAbilities.Items)
	{
		if (Spec.InputID == InputID)
		{
			if (Spec.Ability)
			{
				Spec.InputPressed = true;
				if (Spec.IsActive())
				{
					if (Spec.Ability->bReplicateInputDirectly && IsOwnerActorAuthoritative() == false)
					{
						ServerSetInputPressed(Spec.Handle);
					}

					AbilitySpecInputPressed(Spec);

					// Invoke the InputPressed event. This is not replicated here. If someone is listening, they may replicate the InputPressed event to the server.
					InvokeReplicatedEvent(EAbilityGenericReplicatedEvent::InputPressed, Spec.Handle, Spec.ActivationInfo.GetActivationPredictionKey());
				}
				else
				{
					UGSGameplayAbility* GA = Cast<UGSGameplayAbility>(Spec.Ability);
					if (GA && GA->bActivateOnInput)
					{
						// Ability is not active, so try to activate it
						TryActivateAbility(Spec.Handle);
					}
				}
			}
		}
	}
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-granting"></a>
#### 4.6.3 授予Ability

向`ASC`授予`GameplayAbility`会将其添加到`ASC`的`ActivatableAbilities`列表, 从而允许其在满足[`GameplayTag`需求](#concepts-ga-tags)时激活该`GameplayAbility`.  

我们在服务端授予`GameplayAbility`, 之后其会自动同步[GameplayAbilitySpec](#concepts-ga-spec)到所属(Owning)客户端, 其他客户端/Simulated proxy不会接受到`GameplayAbilitySpec`.  

样例项目在游戏开始时将`TArray<TSubclassOf<UGDGameplayAbility>>`保存在它读取和授予的`Character`类中.  

```c++
void AGDCharacterBase::AddCharacterAbilities()
{
	// Grant abilities, but only on the server	
	if (Role != ROLE_Authority || !AbilitySystemComponent.IsValid() || AbilitySystemComponent->CharacterAbilitiesGiven)
	{
		return;
	}

	for (TSubclassOf<UGDGameplayAbility>& StartupAbility : CharacterAbilities)
	{
		AbilitySystemComponent->GiveAbility(
			FGameplayAbilitySpec(StartupAbility, GetAbilityLevel(StartupAbility.GetDefaultObject()->AbilityID), static_cast<int32>(StartupAbility.GetDefaultObject()->AbilityInputID), this));
	}

	AbilitySystemComponent->CharacterAbilitiesGiven = true;
}
```

当授予这些`GameplayAbility`时, 我们就在使用`UGameplayAbility`类, Ability等级, 其绑定的输入和`SourceObject`或将该`GameplayAbility`设置到该`ASC`的Source(源)创建`GameplayAbilitySpec`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-activating"></a>
#### 4.6.4 激活Ability

如果某个`GameplayAbility`被分配给了一个输入事件, 那么当输入按键按下并且它的`GameplayTag`需求满足时, 它将会自动激活, 这可能并非总是激活`GameplayAbility`的期望方式. `ASC`提供了另外四种激活`GameplayAbility`的方法: 通过`GameplayTag`, `GameplayAbility`类, `GameplayAbilitySpecHandle`和Event, 通过Event激活`GameplayAbility`允许你[传递一个该事件的数据负载(Payload)](#concepts-ga-data).  

```c++
UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilitiesByTag(const FGameplayTagContainer& GameplayTagContainer, bool bAllowRemoteActivation = true);

UFUNCTION(BlueprintCallable, Category = "Abilities")
bool TryActivateAbilityByClass(TSubclassOf<UGameplayAbility> InAbilityToActivate, bool bAllowRemoteActivation = true);

bool TryActivateAbility(FGameplayAbilitySpecHandle AbilityToActivate, bool bAllowRemoteActivation = true);

bool TriggerAbilityFromGameplayEvent(FGameplayAbilitySpecHandle AbilityToTrigger, FGameplayAbilityActorInfo* ActorInfo, FGameplayTag Tag, const FGameplayEventData* Payload, UAbilitySystemComponent& Component);

FGameplayAbilitySpecHandle GiveAbilityAndActivateOnce(const FGameplayAbilitySpec& AbilitySpec);
```

想要通过Event激活`GameplayAbility`, `GameplayAbility`必须设置它的`Trigger`, 分配一个`GameplayTag`并为`GameplayEvent`选择一个选项. 想要发送Event, 就得使用`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`函数. 通过Event激活`GameplayAbility`允许你传递一个数据负载(Payload).  

`GameplayAbility Trigger`也允许你在某个`GameplayTag`添加或移除时激活该`GameplayAbility`.  

**Note:** 当从蓝图中的Event激活`GameplayAbility`时, 你必须使用`ActivateAbilityFromEvent`节点, 并且标准的`ActivateAbility`节点不能出现在图表中, 如果`ActivateAbility`节点存在, 它就会一直被调用而不调用`ActivateAbilityFromEvent`节点.  

**Note:** 不要忘记应该在`GameplayAbility`终止时调用`EndAbility()`, 除非你的`GameplayAbility`是像被动技能那样一直运行的`GameplayAbility`.  

对于**客户端预测**`GameplayAbility`的激活序列:  

1. **所属(Owning)客户端**调用`TryActivateAbility()`
2. 调用`InternalTryActivateAbility()`
3. 调用`CanActivateAbility()`并返回是否满足`GameplayTag`需求, `ASC`是否满足技能花费, `GameplayAbility`是否不在冷却期和当前是否没有其他实例被激活
4. 调用`CallServerTryActivateAbility()`并传入其生成的`Prediction Key`
5. 调用`CallActivateAbility()`
6. 调用`PreActivate()`, Epic称之为"boilerplate init stuff"
7. 调用`ActivateAbility()`最终激活Ability

**服务端**接收到`CallServerTryActivateAbility()`  

1. 调用`ServerTryActivateAbility()`
2. 调用`InternalServerTryActivateAbility()`
3. 调用`InternalTryActivateAbility()`
4. 调用`CanActivateAbility()`并返回是否满足`GameplayTag`需求, `ASC`是否满足技能花费, `GameplayAbility`是否不在冷却期和当前是否没有其他实例被激活
5. 如果成功则调用`ClientActivateAbilitySucceed()`告知客户端更新它的`ActivationInfo`(即该激活已由服务端确认)并广播`OnConfirmDelegate`代理. 这和输入确认(Input Confirmation)不一样.
6. 调用`CallActivateAbility()`
7. 调用`PreActivate()`, Epic称之为"boilerplate init stuff"
8. 调用`ActivateAbility()`最终激活Ability

如果服务端在任意时刻激活失败, 就会调用`ClientActivateAbilityFailed()`, 立即终止客户端的`GameplayAbility`并撤销所有预测的修改.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-activating-passive"></a>
##### 4.6.4.1 被动Ability

为了实现自动激活和持续运行的被动`GameplayAbility`, 需要重写`UGameplayAbility::OnAvatarSet()`, 该函数在授予`GameplayAbility`并设置`AvatarActor`且调用`TryActivateAbility()`时自动调用.  

我建议添加一个`布尔值`到你的自定义`UGameplayAbility`类来表明其在授予时是否应该被激活. 样例项目中的被动护甲叠层Ability是这样做的.  

被动`GameplayAbility`一般有一个`仅服务器(Server Only)`的[网络执行策略(Net Execution Policy)](#concepts-ga-net).  

```c++
void UGDGameplayAbility::OnAvatarSet(const FGameplayAbilityActorInfo * ActorInfo, const FGameplayAbilitySpec & Spec)
{
	Super::OnAvatarSet(ActorInfo, Spec);

	if (ActivateAbilityOnGranted)
	{
		bool ActivatedAbility = ActorInfo->AbilitySystemComponent->TryActivateAbility(Spec.Handle, false);
	}
}
```

Epic描述该函数为初始化被动Ability的正确位置和应该做一些类似`BeginPlay`的事情.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-cancelabilities"></a>
#### 4.6.5 取消Ability

为了从内部取消`GameplayAbility`, 可以调用`CancelAbility()`, 其会调用`EndAbility()`并设置它的`WasCancelled`参数为`true`.  

为了从外部取消`GameplayAbility`, `ASC`提供了一些函数:  

```c++
/** Cancels the specified ability CDO. */
void CancelAbility(UGameplayAbility* Ability);	

/** Cancels the ability indicated by passed in spec handle. If handle is not found among reactivated abilities nothing happens. */
void CancelAbilityHandle(const FGameplayAbilitySpecHandle& AbilityHandle);

/** Cancel all abilities with the specified tags. Will not cancel the Ignore instance */
void CancelAbilities(const FGameplayTagContainer* WithTags=nullptr, const FGameplayTagContainer* WithoutTags=nullptr, UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities regardless of tags. Will not cancel the ignore instance */
void CancelAllAbilities(UGameplayAbility* Ignore=nullptr);

/** Cancels all abilities and kills any remaining instanced abilities */
virtual void DestroyActiveState();
```

**Note:** 我发现如果存在一个`非实例(Non-Instanced)GameplayAbility`时, `CancelAllAbilities`似乎不能正常运行, 它似乎会命中这个`非实例(Non-Instanced)GameplayAbility`并放弃继续处理. `CancelAbility`可以更好地处理`非实例(Non-Instanced)GameplayAbility`, 样例项目就是这样使用的(跳跃是一个非实例(Non-Instanced)GameplayAbility), 因人而异.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-definition-activeability"></a>
#### 4.6.6 获取激活的Ability

初学者经常会问"我怎样才能获取激活的Ability?", 也许是用来设置变量或取消它. 多个`GameplayAbility`可以在同一时刻激活, 因此没有"一个激活的Ability", 相反, 你必须搜索`ASC`的`ActivatableAbility`列表(`ASC`拥有的已授予`GameplayAbility`)并找到一个与你正在寻找的[资源或授予的GameplayTag](#concepts-ga-tags)相匹配的Ability.  

`UAbilitySystemComponent::GetActivatableAbilities()`会返回一个用于遍历的`TArray<FGameplayAbilitySpec>`.  

`ASC`还有另一个有用的函数, 它将一个`GameplayTagContainer`作为参数来协助搜索, 而无需手动遍历`GameplayAbilitySpec`列表. `bOnlyAbilitiesThatSatisfyTagRequirements`参数只会返回那些`GameplayTag`满足需求且可以立刻激活的`GameplayAbilitySpecs`, 例如, 你可能有两个基本的攻击`GameplayAbility`, 一个使用武器, 另一个使用拳头, 正确的激活取决于武器是否装备并设置了`GameplayTag`需求. 详见Epic关于函数的注释.  

```c++
UAbilitySystemComponent::GetActivatableGameplayAbilitySpecsByAllMatchingTags(const FGameplayTagContainer& GameplayTagContainer, TArray < struct FGameplayAbilitySpec* >& MatchingGameplayAbilities, bool bOnlyAbilitiesThatSatisfyTagRequirements = true)
```

一旦你获取到了寻找的`FGameplayAbilitySpec`, 那么就可以调用它的`IsActive()`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-instancing"></a>
#### 4.6.7 实例化策略

`GameplayAbility`的实例化策略决定了当`GameplayAbility`激活时是否和如何实例化.  

|实例化策略|描述|何时使用的例子|
|:-:|:-:|:-:|
|每个Actor实例化(Instanced Per Actor)|每个`ASC`只能有一个在激活之间复用的`GameplayAbility`实例.|这可能是你使用最频繁的实例化策略. 你可以对任一`Ability`使用并在激活之间提供持久化. 设计者可以在激活之间手动重设任意变量.|
|每个操作实例化(Instanced Per Execution)|每有一个`GameplayAbility`激活, 就有一个新的`GameplayAbility`实例创建.|这些`GameplayAbility`的好处是每次激活时变量都会重置, 其性能要比`Instanced Per Actor`差, 因为每次激活时都会生成新的`GameplayAbility`. 样例项目没有使用该方式.|
|非实例化(Non-Instanced)|`GameplayAbility`操作其`ClassDefaultObject`, 没有实例创建.|它是三种方式中性能最好的, 但是使用它是最受限制的. `非实例化(Non-Instanced)GameplayAbility`不能存储状态, 这意味着没有动态变量和不能绑定到`AbilityTask`委托. 使用它的最佳场景就是需要频繁使用的简单Ability, 像MOBA或RTS游戏中小兵的基础攻击. 样例项目中的跳跃`GameplayAbility`就是`非实例化(Non-Instanced)`的.|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-net"></a>
#### 4.6.8 网络执行策略(Net Execution Policy)

`GameplayAbility`的`网络执行策略(Net Execution Policy)`决定了谁该以什么顺序运行该`GameplayAbility`.  

|网络执行策略(Net Execution Policy)|描述|
|:-:|:-:|
|Local Only|`GameplayAbility`只运行在所属(Owning)客户端. 这对那些只需做本地视觉变化的Ability很有用. 单人游戏应该使用`Server Only`.|
|Local Predicted|`Local Predicted GameplayAbility`首先在所属(Owning)客户端激活, 之后在服务端激活. 服务端版本会纠正客户端预测的所有不正确的地方. 参见Prediction.|
|Server Only|`GameplayAbility`只运行在服务端. 被动`GameplayAbility`一般是`Server Only`. 单人游戏应该使用该项.|
|Server Initiated|`Server Initiated GameplayAbility`首先在服务端激活, 之后在所属(Owning)客户端激活. 我个人使用的不多(如果有的话).|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-tags"></a>
#### 4.6.9 Ability标签

`GameplayAbility`自带有内建逻辑的`GameplayTagContainer`. 这些`GameplayTag`都不进行同步.  

|GameplayTagContainer|描述|
|:-:|:-:|
|Ability Tags|`GameplayAbility`拥有的`GameplayTag`, 这只是用来描述`GameplayAbility`的`GameplayTag`.|
|Cancel Abilities with Tag|当该`GameplayAbility`激活时, 其他`Ability Tags`中拥有这些`GameplayTag`的`GameplayAbility`将会被取消.|
|Block Abilities with Tag|当该`GameplayAbility`激活时, 其他`Ability Tags`中拥有这些`GameplayTag`的`GameplayAbility`将会阻塞激活.|
|Activation Owned Tags|当该`GameplayAbility`激活时, 这些`GameplayTag`会交给该`GameplayAbility`的拥有者.|
|Activation Required Tags|该`GameplayAbility`只有在其拥有者拥有所有这些`GameplayTag`时才会激活.|
|Activation Blocked Tags|该`GameplayAbility`在其拥有者拥有任意这些标签时不能被激活.|
|Source Required Tags|该`GameplayAbility`只有在`Source`拥有所有这些`GameplayTag`时才会激活. `Source GameplayTag`只有在该`GameplayAbility`由事件触发时设置.|
|Source Blocked Tags|该`GameplayAbility`在`Source`拥有任意这些标签时不能被激活. `Source GameplayTag`只有在该`GameplayAbility`由事件触发时设置.|
|Target Required Tags|该`GameplayAbility`只有在`Target`拥有所有这些`GameplayTag`时才会激活. `Target GameplayTag`只有在该`GameplayAbility`由事件触发时设置.|
|Target Blocked Tags|该`GameplayAbility`在`Target`拥有任意这些标签时不能被激活. `Target GameplayTag`只有在该`GameplayAbility`由事件触发时设置.|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-spec"></a>
#### 4.6.10 Gameplay Ability Spec

`GameplayAbilitySpec`会在`GameplayAbility`授予后存在于`ASC`中并定义可激活`GameplayAbility` - `GameplayAbility`类, 等级, 输入绑定和必须与`GameplayAbility`类分开保存的运行时状态.  

当`GameplayAbility`在服务端授予时, 服务端会同步`GameplayAbilitySpec`到所属(Owning)客户端, 因此可以激活它.  

激活`GameplayAbilitySpec`会根据它的`实例化策略(Instancing Policy)`创建一个`GameplayAbility`实例(`Non-Instanced GameplayAbility`除外).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-data"></a>
#### 4.6.11 传递数据到Ability

`GameplayAbility`的一般范式是`Activate->Generate Data->Apply->End`. 有时你需要调整现有数据, GAS提供了一些选项来获取外部数据到你的`GameplayAbility`.  

|方法|描述|
|:-:|:-:|
|通过Event激活`GameplayAbility`|使用含有数据负载(Payload)的Event激活`GameplayAbility`. 对于客户端预测的`GameplayAbility`, Event的负载(Payload)是由客户端同步到服务端的. 对于那些不适合任意现有变量的任意数据可以使用两个`Optional Object`或[TargetData](#concepts-targeting-data)变量. 该方法的缺点是不能使用输入绑定激活Ability. 为了通过Event激活`GameplayAbility`, 该`GameplayAbility`必须设置其Trigger, 分配一个`GameplayTag`并选择一个`GameplayEvent`选项. 想要发送事件, 就得使用`UAbilitySystemBlueprintLibrary::SendGameplayEventToActor(AActor* Actor, FGameplayTag EventTag, FGameplayEventData Payload)`函数.|
|使用`WaitGameplayEvent AbilityTask`|在`GameplayAbility`激活后, 使用`WaitGameplayEvent AbilityTask`来告知其监听带有负载(Payload)的事件. Event负载(Payload)及其发送过程与通过Event激活`GameplayAbility`是一样的. 该方法的缺点是Event不能通过`AbilityTask`同步且只能用于`Local Only`和`Server Only`的`GameplayAbility`. 你可以编写自己的`AbilityTask`以支持同步Event负载(Payload).|
|使用`TargetData`|自定义`TargetData`结构体是一种在客户端和服务端之间传递任意数据的好方法.|
|保存数据在`OwnerActor`或者`AvatarActor`|使用保存于`OwnerActor`, `AvatarActor`或者其他任意你可以获取到引用的对象中的可同步变量. 这种方法最灵活且可以用于由输入绑定激活的`GameplayAbility`, 然而, 它不能保证在使用时数据同步, 你必须提前做好准备 - 这意味着如果你设置了一个可同步的变量, 之后立即激活该`GameplayAbility`, 那么因为存在潜在的丢包情况, 不能保证接收者上发生的顺序.|

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-commit"></a>
#### 4.6.12 Ability花费(Cost)和冷却(Cooldown)

`GameplayAbility`默认带有对可选Cost和Cooldown的功能. Cost是`ASC`为了激活使用`即刻(Instant)GameplayEffect`([Cost GE](#concepts-ge-cost))实现的GameplayAbility所必须具有的预先定义的大量Attribute. Cooldown是用于阻止`GameplayAbility`再次激活(直到冷却完成)的计时器, 其由`持续(Duration)GameplayEffect`([Cooldown GE](#concepts-ge-cooldown))实现.  

在`GameplayAbility`调用`UGameplayAbility::Activate()`之前, 其会调用`UGameplayAbility::CanActivateAbility()`, 该函数会检查所属`ASC`是否满足Cost(`UGameplayAbility::CheckCost()`)并确保该`GameplayAbility`不在冷却期(`UGameplayAbility::CheckCooldown()`).  

在`GameplayAbility`调用`Activate()`之后, 其可以选择性使用`UGameplayAbility::CommitAbility()`随时提交Cost和Cooldown, `UGameplayAbility::CommitAbility()`会调用`UGameplayAbility::CommitCost()`和`UGameplayAbility::CommitCooldown()`, 如果它们不需要同时提交, 设计师可以选择分别调用`CommitCost()`或`CommitCooldown()`. 提交Cost和Cooldown会多次调用`CheckCost()`和`CheckCooldown()`. 所属(Owning)`ASC`的Attribute在`GameplayAbility`激活后可能改变, 从而导致提交时无法满足Cost. 如果[prediction key](#concepts-p-key)在提交时有效的话, 提交Cost和Cooldown是可以[客户端预测](#concepts-p)的.  

详见[CostGE](#concepts-ge-cost)和[CooldownGE](#concepts-ge-cooldown).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-leveling"></a>
#### 4.6.13 升级Ability

有两个常见的方法用于升级Ability:  

|升级方法|描述|
|:-:|:-:|
|升级时取消授予和重新授予|自`ASC`取消授予(移除)`GameplayAbility`, 并在下一等级于服务端上重新授予. 如果此时`GameplayAbility`是激活的, 那么就会终止它.|
|增加`GameplayAbilitySpec`的等级|在服务端上找到`GameplayAbilitySpec`, 增加它的等级, 并将其标记为Dirty以同步到所属(Owning)客户端. 该方法不会在`GameplayAbility`激活时将其终止.|

两种方法的主要区别在于你是否想要在升级时取消激活的`GameplayAbility`. 基于你的`GameplayAbility`, 你很可能需要同时使用两种方法. 我建议在`UGameplayAbility`子类中增加一个布尔值以明确使用哪种方法.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-sets"></a>
#### 4.6.14 Ability集合

`GameplayAbilitySet`是很方便的`UDataAsset`类, 其用于保存输入绑定和初始`GameplayAbility`列表, 该`GameplayAbility`列表用于拥有授予`GameplayAbility`逻辑的Character. 子类也可以包含额外的逻辑和属性. Paragon中的每个英雄都拥有一个`GameplayAbilitySet`以包含其授予的所有`GameplayAbility`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-batching"></a>
#### 4.6.15 Ability批处理

一般`GameplayAbility`的生命周期最少涉及2到3个自客户端到服务端的RPC.  

1. CallServerTryActivateAbility()
2. ServerSetReplicatedTargetData() (可选)
3. ServerEndAbility()

如果`GameplayAbility`在一帧同一原子(Atomic)组中执行这些操作, 我们就可以优化该工作流, 将所有2个或3个RPC批处理(整合)为1个RPC. `GAS`称这种RPC优化为`Ability批处理`. 常见的使用`Ability批处理`时机的例子是枪支命中判断. 枪支命中判断时, 会在一帧同一原子(Atomic)组中做射线检测, 发送[TargetData](#concepts-targeting-data)到服务端并结束Ability. GASShooter样例项目对其枪支命中判断使用了该技术.  

半自动枪支是最好的案例, 其将`CallServerTryActivateAbility()`, `ServerSetReplicatedTargetData()`(子弹命中结果)和`ServerEndAbility()`批处理成一个而不是三个`RPC`.  

全自动/爆炸开火枪支会对第一发子弹将`CallServerTryActivateAbility()`和`ServerSetReplicatedTargetData()`批处理成一个而不是两个RPC, 接下来的每一发子弹都是它自己的`ServerSetReplicatedTargetData()`RPC, 最后, 当停止射击时, `ServerEndAbility()`会作为单独的RPC发送. 这是最坏的情况, 我们只保存了第一发子弹的一个RPC而不是两个. 这种情况也可以通过[GameplayEvent](#concepts-ga-data)激活Ability来实现, 该`GameplayEvent`会自客户端向服务端发送带有`EventPayload`的子弹`TargetData`. 后者的缺点是`TargetData`必须在Ability外部生成, 尽管批处理方法在Ability内部生成`TargetData`.  

Ability批处理在[ASC](#concepts-asc)中是默认禁止的. 为了启用Ability批处理, 需要重写`ShouldDoServerAbilityRPCBatch()`以返回true:  

```c++
virtual bool ShouldDoServerAbilityRPCBatch() const override { return true; }
```

现在Ability批处理已经启用, 在激活你想使用批处理的Ability之前, 必须提前创建一个`FScopedServerAbilityRPCBatcher`结构体, 这个特殊的结构体会在其域内尝试批处理所有跟随它的Ability, 一旦出了`FScopedServerAbilityRPCBatcher`域, 所有已激活的Ability将不再尝试批处理. `FScopedServerAbilityRPCBatcher`通过在每个可以批处理的函数中设置特殊代码来运行, 其会拦截RPC的发送并将消息打包进一个批处理结构体, 当出了`FScopedServerAbilityRPCBatcher`后, 它会在`UAbilitySystemComponent::EndServerAbilityRPCBatch()`中自动将该批处理结构体RPC到服务端, 服务端在`UAbilitySystemComponent::ServerAbilityRPCBatch_Internal(FServerAbilityRPCBatch& BatchInfo)`中接收该批处理结构体, `BatchInfo`参数会包含几个flag, 其包括该Ability是否应该结束, 激活时输入是否按下和是否包含`TargetData`. 这是个可以设置断点以确认批处理是否正确运行的好函数. 作为选择, 可以使用`AbilitySystem.ServerRPCBatching.Log 1`来启用特别的Ability批处理日志.  

这个方法只能在C++中完成, 并且只能通过`FGameplayAbilitySpecHandle`来激活Ability.  

```c++
bool UGSAbilitySystemComponent::BatchRPCTryActivateAbility(FGameplayAbilitySpecHandle InAbilityHandle, bool EndAbilityImmediately)
{
	bool AbilityActivated = false;
	if (InAbilityHandle.IsValid())
	{
		FScopedServerAbilityRPCBatcher GSAbilityRPCBatcher(this, InAbilityHandle);
		AbilityActivated = TryActivateAbility(InAbilityHandle, true);

		if (EndAbilityImmediately)
		{
			FGameplayAbilitySpec* AbilitySpec = FindAbilitySpecFromHandle(InAbilityHandle);
			if (AbilitySpec)
			{
				UGSGameplayAbility* GSAbility = Cast<UGSGameplayAbility>(AbilitySpec->GetPrimaryInstance());
				GSAbility->ExternalEndAbility();
			}
		}

		return AbilityActivated;
	}

	return AbilityActivated;
}
```

GASShooter对半自动和全自动枪支使用了相同的批处理`GameplayAbility`, 并没有直接调用`EndAbility()`(它通过一个只能由客户端调用的Ability在该Ability外处理, 该只能由客户端调用的Ability用于管理玩家输入和基于当前开火模式对批处理Ability的调用). 因为所有的RPC必须在`FScopedServerAbilityRPCBatcher`域中, 所以我提供了`EndAbilityImmediately`参数以使仅客户端的控制/管理可以明确该Ability是否可以批处理`EndAbility()`(半自动)或不批处理`EndAbility()`(全自动)以及之后`EndAbility()`是否可以在其自己的RPC中调用.  

GASShooter暴露了一个蓝图节点以允许上文提到的仅客户端调用的Ability所使用的批处理Ability来触发批处理Ability. (译者注: 此处相当拗口, 但原文翻译确实如此, 配合项目浏览也许会更容易明白些.)  

![Activate Batched Ability](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/batchabilityactivate.png)  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-ga-netsecuritypolicy"></a>
#### 4.6.16 网络安全策略(Net Security Policy)

`GameplayAbility`的网络安全策略决定了Ability应该在网络的何处执行. 它为尝试执行限制Ability的客户端提供了保护.  

|网络安全策略|描述|
|:-:|:-:|
|ClientOrServer|没有安全需求. 客户端或服务端可以自由地触发该Ability的执行和终止.|
|ServerOnlyExecution|客户端对该Ability请求的执行会被服务端忽略, 但客户端仍可以请求服务端取消或结束该Ability.|
|ServerOnlyTermination|客户端对该Ability请求的取消或结束会被服务端忽略, 但客户端仍可以请求执行该Ability.|
|ServerOnly|服务端控制该Ability的执行和终止, 客户端的任何请求都会被忽略.|

<a name="concepts-at"></a>
### 4.7 Ability Tasks

<a name="concepts-at-definition"></a>
#### 4.7.1 AbilityTask定义

`GameplayAbility`只能在一帧中执行, 这本身并不能提供太多的灵活性, 为了实现随时间推移触发或响应经过一段时间后触发的委托操作, 我们需要使用`AbilityTask`.  

GAS自带很多`AbilityTask`:  

* 使用`RootMotionSource`移动Character的Task
* 播放动画蒙太奇的Task
* 响应`Attribute`变化的Task
* 响应`GameplayEffect`变化的Task
* 响应玩家输入的Task
* 更多

`UAbilityTask`在构造函数中强制硬编码最多允许1000个同时运行的`AbilityTask`, 当设计那些同时拥有数百个Character的游戏(像RTS)的`GameplayAbility`时要注意这一点.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-at-definition"></a>
#### 4.7.2 自定义AbilityTask

通常你需要创建自己的自定义`AbilityTask`(C++中). 样例项目带有两个自定义`AbilityTask`:  

1. `PlayMontageAndWaitForEvent`是默认`PlayMontageAndWait`和`WaitGameplayEvent`AbilityTask的结合体, 其允许动画蒙太奇自`AnimNotify`发送GameplayEvent回到启动它的`GameplayAbility`, 可以使用该Task在动画蒙太奇的某个特定时刻来触发操作.
2. `WaitReceiveDamage`可以监听`OwnerActor`接收伤害. 当英雄接收到一个伤害实例时, 被动护甲层`GameplayAbility`就会移除一层护甲.  

`AbilityTask`的组成:  

* 创建新的`AbilityTask`实例的静态函数
* 当`AbilityTask`完成目的时分发的委托(Delegate)
* 进行主要工作的`Activate()`函数, 绑定到外部的委托等等
* 进行清理工作的`OnDestroy()`函数, 包括其绑定到外部的委托
* 所有绑定到外部委托的回调函数
* 成员变量和所有内部辅助函数

**Note:** `AbilityTask`只能声明一种类型的输出委托, 所有的输出委托都必须是该种类型, 不管它们是否使用参数. 对于未使用的委托参数会传递默认值.  

`AbilityTask`只能运行在那些运行所属`GameplayAbility`的客户端或服务端, 然而, 可以通过设置`bSimulatedTask = true`使`AbilityTask`运行在Simulated Client上, 在`AbilityTask`的构造函数中, 重写`virtual void InitSimulatedTask(UGameplayTasksComponent& InGameplayTasksComponent);`并将所有成员变量设置为同步的, 这只在极少的情况下有用, 比如在移动`AbilityTask`中, 不想同步每次移动变化, 但是又需要模拟整个移动`AbilityTask`, 所有的`RootMotionSource AbilityTask`都是这样做的, 查看`AbilityTask_MoveToLocation.h/.cpp`以作为参考范例.  

如果你在`AbilityTask`的构造函数中设置了`bTickingTask = true;`并重写了`virtual void TickTask(float DeltaTime);`, `AbilityTask`就可以使用`Tick`, 这在你需要根据帧率平滑线性插值的时候很有用. 查看`AbilityTask_MoveToLocation.h/.cpp`以作为参考范例.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-at-using"></a>
#### 4.7.3 使用AbilityTask

在C++中创建并激活`AbilityTask`(GDGA_FireGun.cpp):  

```c++
UGDAT_PlayMontageAndWaitForEvent* Task = UGDAT_PlayMontageAndWaitForEvent::PlayMontageAndWaitForEvent(this, NAME_None, MontageToPlay, FGameplayTagContainer(), 1.0f, NAME_None, false, 1.0f);
Task->OnBlendOut.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnCompleted.AddDynamic(this, &UGDGA_FireGun::OnCompleted);
Task->OnInterrupted.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->OnCancelled.AddDynamic(this, &UGDGA_FireGun::OnCancelled);
Task->EventReceived.AddDynamic(this, &UGDGA_FireGun::EventReceived);
Task->ReadyForActivation();
```

在蓝图中, 我们只需使用为`AbilityTask`创建的蓝图节点, 不必调用`ReadyForActivate()`, 其由`Engine/Source/Editor/GameplayTasksEditor/Private/K2Node_LatentGameplayTaskCall.cpp`自动调用. `K2Node_LatentGameplayTaskCall`也会自动调用`BeginSpawningActor()`和`FinishSpawningActor()`(如果它们存在于你的`AbilityTask`类中, 查看`AbilityTask_WaitTargetData`), 再强调一遍, `K2Node_LatentGameplayTaskCall`只会对蓝图做这些自动操作, 在C++中, 我们必须手动调用`ReadyForActivation()`, `BeginSpawningActor()`和`FinishSpawningActor()`.  

![Blueprint WaitTargetData AbilityTask](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/abilitytask.png)  

如果想要手动取消`AbilityTask`, 只需在蓝图(Async Task Proxy)或C++中对`AbilityTask`对象调用`EndTask()`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-at-rms"></a>
#### 4.7.4 Root Motion Source Ability Task

GAS自带的`AbilityTask`可以使用挂载在`CharacterMovementComponent`中的`Root Motion Source`随时间推移而移动`Character`, 像击退, 复杂跳跃, 吸引和猛冲.  

**Note:** `RootMotionSource AbilityTask`预测支持的引擎版本是4.19和4.25+, 该预测在引擎版本4.20-4.24中存在bug, 然而, `AbilityTask`仍然可以使用较小的网络修正在多人游戏中执行功能, 并且在单人游戏中完美运行. 可以将4.25中对预测的修复自定义到4.20~4.24引擎中.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc"></a>
### 4.8 Gameplay Cues

<a name="concepts-gc-definition"></a>
#### 4.8.1 GameplayCue定义

`GameplayCue(GC)`执行非游戏逻辑相关的功能, 像音效, 粒子效果, 镜头抖动等等. `GameplayCue`一般是可同步(除非在客户端明确`执行(Executed)`, `添加(Added)`和`移除(Removed)`)和可预测的.  

我们可以在`ASC`中通过发送一个**强制带有"GameplayCue"父名**的相应`GameplayTag`和`GameplayCueManager`的事件类型(Execute, Add或Remove)来触发`GameplayCue`. `GameplayCueNotify`对象和其他实现`IGameplayCueInterface`的`Actor`可以基于`GameplayCue`的`GameplayTag(GameplayCueTag)`来订阅(Subscribe)这些事件.  

**Note:** 再次强调, `GameplayCue`的`GameplayTag`需要以`GameplayCue`的父`GameplayTag`为开头, 举例来说, 一个有效的`GameplayCue`的`GameplayTag`可能是`GameplayCue.A.B.C`.  

有两个`GameplayCueNotify`类, `Static`和`Actor`. 它们各自响应不同的事件, 并且不同的`GameplayEffect`类型可以触发它们. 根据你的逻辑重写相关的事件.  

|GameplayCue类|事件|GameplayEffect类型|描述|
|:-:|:-:|:-:|:-:|
|[GameplayCueNotify_Static](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UGameplayCueNotify_Static/index.html)|Execute|Instant或Periodic|`Static GameplayCueNotify`直接操作`ClassDefaultObject`(意味着没有实例)并且对于一次性效果(像击打伤害)是极好的.|
|[GameplayCueNotify_Actor](https://docs.unrealengine.com/en-US/BlueprintAPI/GameplayCueNotify/index.html)|Add或Remove|Duration或Infinite|`Actor GameplayCueNotify`会在添加(Added)时生成一个新的实例, 因为其是实例化的, 所以可以随时间推移执行操作直到被移除(Removed). 这对循环的声音和粒子效果是很好的, 其会在`持续(Duration)`或`无限(Infinite)`GameplayEffect被移除或手动调用移除时移除. 其也自带选项来管理允许同时添加(Added)多少个, 因此多个相同效果的应用只启用一次声音或例子效果.|

`GameplayCueNotify`技术上可以响应任何事件, 但是这是我们一般使用它的方式.  

**Note:** 当使用`GameplayCueNotify_Actor`时, 要勾选`Auto Destroy on Remove`, 否则接下来对`GameplayCueTag`的`添加(Add)`调用将无法运行.  

当使用除了`Full`, `Add`和`Remove`的`ASC`[同步模式](#concepts-asc-rm)时, `GC`事件会在服务端玩家中触发两次(Listen Server) - 一次是应用`GE`, 再一次是从"Minimal"`NetMultiCast`到客户端. 然而, `WhileActive`事件会仍会只触发一次. 所有的事件在客户端中只触发一次.  

样例项目包含了一个`GameplayCueNotify_Actor`用于眩晕和奔跑效果. 其还含有一个`GameplayCueNotify_Static`用于枪支弹药伤害. 这些`GC`可以通过[客户端触发](#concepts-gc-local)来进行优化, 而不是通过`GE`同步. 我选择了在样例项目中展示使用它们的基本方法.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-trigger"></a>
#### 4.8.2 触发GameplayCue

当`GameplayEffect`成功应用时(未被Tag或免疫(Immunity)阻塞), 从其中填充(Fill)所有应该被触发的`GameplayCue`的`GameplayTag`.  

![GameplayCue Triggered from a GameplayEffect](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gcfromge.png)  

`UGameplayAbility`提供了蓝图节点来`Execute`, `Add`或`Remove GameplayCue`.  

![GameplayCue Triggered from a GameplayAbility](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gcfromga.png)  

在C++中, 你可以在`ASC`中直接调用函数(或者在你的`ASC`子类中暴露它们到蓝图):  

```c++
/** GameplayCues can also come on their own. These take an optional effect context to pass through hit result, etc */
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void ExecuteGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Add a persistent gameplay cue */
void AddGameplayCue(const FGameplayTag GameplayCueTag, FGameplayEffectContextHandle EffectContext = FGameplayEffectContextHandle());
void AddGameplayCue(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

/** Remove a persistent gameplay cue */
void RemoveGameplayCue(const FGameplayTag GameplayCueTag);
	
/** Removes any GameplayCue added on its own, i.e. not as part of a GameplayEffect. */
void RemoveAllGameplayCues();
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-local"></a>
#### 4.8.3 客户端GameplayCue

从`GameplayAbility`和`ASC`中暴露的用于触发`GameplayCue`的函数默认是可同步的. 每个`GameplayCue`事件都是一个多播(Multicast)RPC. 这会导致大量RPC. GAS也强制在每次网络更新中最多能有两个相同的`GameplayCue`RPC. 我们可以通过使用客户端`GameplayCue`来避免这个问题. 客户端`GameplayCue`只能在单独的客户端上`Execute`, `Add`或`Remove`.  

可以使用客户端`GameplayCue`的场景:  

* 抛射物伤害
* 近战碰撞伤害
* 自动画蒙太奇触发的`GameplayCue`

你应该添加到`ASC`子类中的客户端`GameplayCue`函数:  

```c++
UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);

UFUNCTION(BlueprintCallable, Category = "GameplayCue", Meta = (AutoCreateRefTerm = "GameplayCueParameters", GameplayTagFilter = "GameplayCue"))
void RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters& GameplayCueParameters);
```

```c++
void UPAAbilitySystemComponent::ExecuteGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Executed, GameplayCueParameters);
}

void UPAAbilitySystemComponent::AddGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::OnActive, GameplayCueParameters);
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::WhileActive, GameplayCueParameters);
}

void UPAAbilitySystemComponent::RemoveGameplayCueLocal(const FGameplayTag GameplayCueTag, const FGameplayCueParameters & GameplayCueParameters)
{
	UAbilitySystemGlobals::Get().GetGameplayCueManager()->HandleGameplayCue(GetOwner(), GameplayCueTag, EGameplayCueEvent::Type::Removed, GameplayCueParameters);
}
```

如果某个`GameplayCue`是客户端添加的, 那么它也应该自客户端移除. 如果它是通过同步添加的, 那么它也应该通过同步移除.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-parameters"></a>
#### 4.8.4 GameplayCue参数

`GameplayCue`接受一个包含额外`GameplayCue `信息的`FGameplayCueParameters`结构体作为参数. 如果你在`GameplayAbility`或`ASC`中使用函数手动触发`GameplayCue`, 那么就必须手动填充传递给`GameplayCue`的`GameplayCueParameters`结构体. 如果`GameplayCue`由`GameplayEffect`触发, 那么下列的变量会自动填充到`FGameplayCueParameters`结构体中:  

* AggregatedSourceTags
* AggregatedTargetTags
* GameplayEffectLevel
* AbilityLevel
* [EffectContext](#concepts-ge-context)
* Magnitude(如果`GameplayEffect`有一个在`GameplayCue`标签容器(TagContainer)之上的下拉列表(Dropdown)中用于Magnitude选择的`Attribute`, 并且有一个相关的影响该`Attribute`的`Modifier`)

当手动触发`GameplayCue`时, `GameplayCueParameters`中的`SourceObject`变量似乎是一个传递任意数据到`GameplayCue`的好地方.  

**Note:** 参数结构体中的某些变量, 像`Instigator`, 可能已经存在于`EffectContext`中. `EffectContext`也可以包含`FHitResult`用于存储`GameplayCue`在世界中生成的位置. 子类化`EffectContext`似乎是一个传递更多数据到`GameplayCue`的好方法, 特别是对于那些由`GameplayEffect`触发的`GameplayCue`.  

查看[UAbilitySystemGlobals](#concepts-asg)中用于填充`GameplayCueParameters`结构体的三个函数以获得更多信息. 它们是虚函数, 因此你可以重写它们以自动填充更多信息.  

```c++
/** Initialize GameplayCue Parameters */
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectSpecForRPC &Spec);
virtual void InitGameplayCueParameters_GESpec(FGameplayCueParameters& CueParameters, const FGameplayEffectSpec &Spec);
virtual void InitGameplayCueParameters(FGameplayCueParameters& CueParameters, const FGameplayEffectContextHandle& EffectContext);
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-manager"></a>
#### 4.8.5 Gameplay Cue Manager

默认情况下, 游戏开始时`GameplayCueManager`会扫描游戏的全部目录以寻找`GameplayCueNotify`并将其加载进内存. 我们可以设置`DefaultGame.ini`来修改`GameplayCueManager`的扫描路径.  

```c++
[/Script/GameplayAbilities.AbilitySystemGlobals]
GameplayCueNotifyPaths="/Game/GASDocumentation/Characters"
```

我们确实想要`GameplayCueManager`扫描并找到所有的`GameplayCueNotify`, 然而, 我们不想要它异步加载每一个, 因为这会将每个`GameplayCueNotify`和它们所引用的音效和例子特效放入内存而不管它们是否在关卡中使用. 在像Paragon这样的大型游戏中, 内存中会放入数百兆的无用资源并造成卡顿和启动时无响应.  

在启动时异步加载每个`GameplayCue`的一种可选方法是只异步加载那些会在游戏中触发的`GameplayCue`, 这会在异步加载每个`GameplayCue`时减少不必要的内存占用和潜在的游戏无响应几率, 从而避免特定`GameplayCue`在游戏中第一次触发时可能出现的延迟效果. SSD不存在这种潜在的延迟, 我还没有在HDD上测试过, 如果在UE编辑器中使用该选项并且编辑器需要编译例子系统的话, 就可能会在GameplayCue首次加载时有轻微的卡顿或无响应, 这在构建版本中不是问题, 因为粒子系统肯定是编译好的.  

首先我们必须继承`UGameplayCueManager`并告知`AbilitySystemGlobals`类在`DefaultGame.ini`中使用我们的`UGameplayCueManager`子类.  

```c++
[/Script/GameplayAbilities.AbilitySystemGlobals]
GlobalGameplayCueManagerClass="/Script/ParagonAssets.PBGameplayCueManager"
```

在我们的`UGameplayCueManager`子类中, 重写`ShouldAsyncLoadRuntimeObjectLibraries()`.  

```c++
virtual bool ShouldAsyncLoadRuntimeObjectLibraries() const override
{
	return false;
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-prevention"></a>
#### 4.8.6 阻止GameplayCue响应

有时我们不想响应`GameplayCue`, 例如我们阻止了一次攻击, 可能就不想播放附加在伤害`GameplayEffect`上的击打效果或者自定义的效果. 我们可以在[GameplayEffectExecutionCalculations](#concepts-ge-ec)中调用`OutExecutionOutput.MarkGameplayCuesHandledManually()`, 之后手动发送我们的`GameplayCue`事件到`Target`或`Source`的`ASC`中.  

如果你想某个特别指定`ASC`中的`GameplayCue`永不触发, 可以设置`AbilitySystemComponent->bSuppressGameplayCues = true;`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-batching"></a>
#### 4.8.7 GameplayCue批处理

每次`GameplayCue`触发都是一次不可靠的多播(NetMulticast)RPC. 在同一时刻触发多个`GameplayCue`的情况下, 有一些优化方法来将它们压缩成一个RPC或者通过发送更少的数据来节省带宽.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-batching-manualrpc"></a>
##### 4.8.7.1 手动RPC

假设你有一个可以发射8枚弹丸的霰弹枪, 就会有8个轨迹和碰撞`GameplayCue`. GASShooter采用将它们联合成一个RPC的延迟(Lazy)方法, 其将所有的轨迹信息保存到[EffectContext](#concepts-ge-ec)作为[TargetData](#concepts-targeting-data). 尽管其将RPC数量从8降为1, 然而还是在这一个RPC中通过网络发送大量数据(~500 bytes). 一个进一步优化的方法是使用一个自定义结构体发送RPC, 在该自定义RPC中你需要高效编码命中位置(Hit Location)或者给一个随机种子以在接收端重现/近似计算碰撞位置, 客户端之后需要解包该自定义结构体并重现[客户端执行的`GameplayCue`](#concepts-gc-local).  

运行机制:  

1. 声明一个`FScopedGameplayCueSendContext`. 其会阻塞`UGameplayCueManager::FlushPendingCues()`直到其出域, 意味着所有`GameplayCue`都将会排队等候直到该`FScopedGameplayCueSendContext`出域.
2. 重写`UGameplayCueManager::FlushPendingCues()`以将那些可以基于一些自定义`GameplayTag`批处理的`GameplayCue`合并进自定义的结构体并将其RPC到客户端.
3. 客户端接收自定义结构体并将其解包进客户端执行的`GameplayCue`.

该方法也可以在你的`GameplayCue`需要特别指定的参数时使用, 这些需要特别指定的参数不能由`GameplayCueParameter`提供, 并且你不想将它们添加到`EffectContext`, 像伤害数值, 暴击标识, 破盾标识, 处决标识等等.  

[https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager](https://forums.unrealengine.com/development-discussion/c-gameplay-programming/1711546-fscopedgameplaycuesendcontext-gameplaycuemanager)  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-gc-batching-gcsonge"></a>
##### 4.8.7.2 GameplayEffect中的多个GameplayCue

一个`GameplayEffect`中的所有`GameplayCue`已经由一个RPC发送了. 默认情况下, `UGameplayCueManager::InvokeGameplayCueAddedAndWhileActive_FromSpec()`会在不可靠的多播(NetMulticast)RPC中发送整个`GameplayEffectSpec`(除了转换为`FGameplayEffectSpecForRPC`)而不管`ASC`的同步模式, 取决于`GameplayEffectSpec`的内容, 这可能会使用大量带宽, 我们可以通过设置`AbilitySystem.AlwaysConvertGESpecToGCParams 1`来将其优化, 这会将`GameplayEffectSpec`转换为`FGameplayCueParameter`结构体并且RPC它而不是整个`FGameplayEffectSpecForRPC`, 这会节省带宽但是只有较少的信息, 取决于`GESpec`如何转换为`GameplayCueParameters`和你的`GameplayCue`需要知道什么.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-asg"></a>
### 4.9 Ability System Globals

[AbilitySystemGlobals](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/UAbilitySystemGlobals/index.html)类保存有关GAS的全局信息. 大多数变量可以在`DefaultGame.ini`中设置. 一般你不需要和该类互动, 但是应该知道它的存在. 如果你需要继承像[GameplayCueManager](#concepts-gc-manager)或[GameplayEffectContext](#concepts-ge-context)这样的对象, 就必须通过`AbilitySystemGlobals`来做.  

想要继承`AbilitySystemGlobals`, 需要在`DefaultGame.ini`中设置类名:  

```c++
[/Script/GameplayAbilities.AbilitySystemGlobals]
AbilitySystemGlobalsClassName="/Script/ParagonAssets.PAAbilitySystemGlobals"
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-asg-initglobaldata"></a>
#### 4.9.1 InitGlobalData()

从UE 4.24开始, 必须调用`UAbilitySystemGlobals::InitGlobalData()`来使用[TargetData](#concepts-targeting-data), 否则你会遇到关于`ScriptStructCache`的错误, 并且客户端会从服务端断开连接, 该函数只需要在项目中调用一次. Fortnite从AssetManager类的起始加载函数中调用该函数, Paragon是从UEngine::Init()中调用的. 我发现将其放到`UEngineSubsystem::Initialize()`是个好位置, 这也是样例项目中使用的. 我觉得你应该复制这段模板代码到你自己的项目中以避免出现`TargetData`的使用问题.  

如果你在使用`AbilitySystemGlobals GlobalAttributeSetDefaultsTableNames`时发生崩溃, 可能之后需要像Fortnite一样在`AssetManager`或`GameInstance`中调用`UAbilitySystemGlobals::InitGlobalData()`而不是在`UEngineSubsystem::Initialize()`中. 该崩溃可能是由于`Subsystem`的加载顺序引发的, `GlobalAttributeDefaultsTables`需要加载`EditorSubsystem`来绑定`UAbilitySystemGlobals::InitGlobalData()`中的委托.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-p"></a>
### 4.10 预测(Prediction)

GAS带有开箱即用的客户端预测功能, 然而, 它不能预测所有. GAS中的客户端预测意思是客户端无需等待服务端的许可而激活`GameplayAbility`和应用`GameplayEffect`. 它可以"预测"许可其可以这样做的服务端和预测其应用`GameplayEffect`的目标. 服务端在客户端激活之后运行`GameplayAbility`(网络延迟)并告知客户端它的预测是否正确, 如果客户端的预测出错, 那么它就会"回滚"其"错误预测"的修改以匹配服务端.  

GAS相关预测的最佳源码是插件源码中的`GameplayPrediction.h`.  

Epic的理念是只能预测"不受伤害(get away with)"的事情. 例如, Paragon和Fortnite不能预测伤害值, 它们很可能对伤害值使用了[ExecutionCalculations](#concepts-ge-ec), 而这无论如何是不能预测的. 这并不是说你不能试着预测像伤害值这样的事情, 无论如何, 如果你这样做了并且效果很好, 那就是极好的.  

> ... we are also not all in on a "predict everything: seamlessly and automatically" solution. We still feel player prediction is best kept to a minimum (meaning: predict the minimum amount of stuff you can get away with).

*来自Epic的Dave Ratti在新的[网络预测插件](#concepts-p-npp)中的注释.*  

**什么是可预测的:**  

* Ability激活
* 触发事件
* GameplayEffect应用
	+ Attribute修改(例外: 执行(Execution)目前无法预测, 只能是Attribute Modifiy)
	+ GameplayTag修改
* GameplayCue事件(可预测GameplayEffect中的和它们自己)
* 蒙太奇
* 移动(内建在UE4 UCharacterMovement中)

**什么是不可预测的:**  

* GameplayEffect移除
* GameplayEffect周期效果(dots ticking)

*源自`GameplayPrediction.h`*  

尽管我们可以预测`GameplayEffect`的应用, 但是不能预测`GameplayEffect`的移除. 绕过这条限制的一个方法是当想要移除`GameplayEffect`时, 可以预测性地执行相反的效果, 假设我们要降低40%移动速度, 可以通过应用增加40%移动速度的buff来预测性地将其移除, 之后同时移除这两个`GameplayEffect`. 这并不是对每个场景都适用, 因此仍然需要对预测性地移除`GameplayEffect`的支持. 来自Epic的Dave Ratti已经表达过在[GAS的迭代版本中](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)增加它的期望.  

因为我们不能预测`GameplayEffect`的移除, 所以就不能完全预测`GameplayAbility`的冷却时间, 并且也没有相反的`GameplayEffect`这种变通方案可供使用. 服务端同步的`Cooldown GE`将会存于客户端上, 并且任何对其绕过的尝试(例如使用`Minimal`同步模式)都会被服务端拒绝. 这意味着高延迟的客户端会花费较长事件来告知服务端开始冷却和接收到服务端`Cooldown GE`的移除. 这意味着高延迟的玩家会比低延迟的玩家有更低的触发率, 从而劣势于低延迟玩家. Fortnite通过使用自定义Bookkeeping而不是`Cooldown GE`的方法避免了该问题.  

关于预测伤害值, 我个人不建议使用, 尽管它是大多数刚接触GAS的人最先做的事情之一, 我特别不建议尝试预测死亡, 虽然你可以预测伤害值, 但是这样做很棘手. 如果你错误预测地应用了伤害, 那么玩家将会看到敌人的生命值会跳动, 如果你尝试预测死亡, 那么这将会是特别尴尬和令人沮丧的, 假设你错误预测了某个`Character`的死亡, 那么它就会开启布娃娃模拟, 只有当服务端纠正后才会停止布娃娃模拟并继续向你射击.

**Note:** 修改`Attribute`的`即刻(Instant)GameplayEffect`(像`Cost GE`)在你自身可以无缝预测, 预测修改其他Character的`即刻(Instant)Attribute`会显示短暂的异常或者`Attribute`中的暂时性问题. 预测的`即刻(Instant)GameplayEffect`实际上被视为`无限(Infinite)GameplayEffect`, 因此如果错误预测的话还可以回滚. 当服务端的`GameplayEffect`被应用时, 其实是存在两个相同的`GameplayEffect`的, 会在短时间内造成`Modifier`应用两次或者不应用, 其最终会纠正自身, 但是有时候这个异常现象对玩家来说是显著的.  

GAS的预测实现尝试解决的问题:  

> 1. "我能这样做吗?"(Can I do this?) 预测的基本协议.
> 2. "撤销"(Undo) 当预测错误时如何消除其副作用.
> 3. "重现"(Redo) 如何避免已经在客户端预测但还是从服务端同步的重播副作用.
> 4. "完整"(Completeness) 如何确认我们真的预测了所有副作用.
> 5. "依赖"(Dependencies) 如何管理依赖预测和预测事件链.
> 6. "重写"(Override) 如何预测地重写由服务端同步/拥有的状态.

源自`GameplayPrediction.h`  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-p-key"></a>
#### 4.10.1 Prediction Key

GAS的预测建立在`Prediction Key`的概念上, 其是一个由客户端激活`GameplayAbility`时生成的整形标识符.  

* 客户端激活`GameplayAbility`时生成`Prediction Key`, 这是`Activation Prediction Key`.  
* 客户端使用`CallServerTryActivateAbility()`将该`Prediction Key`发送到服务端.
* 客户端在`Prediction Key`有效时将其添加到应用的所有`GameplayEffect`.
* 客户端的`Prediction Key`出域. 之后该`GameplayAbility`中的预测效果(Effect)需要一个新的[Scoped Prediction Window](#concepts-p-windows).
* 服务端从客户端接收`Prediction Key`.
* 服务端将`Prediction Key`添加到其应用的所有`GameplayEffect`.
* 服务端同步该`Prediction Key`回客户端.
* 客户端使用`Prediction Key`从服务端接收同步的`GameplayEffect`, 该`Prediction Key`用于应用`GameplayEffect`. 如果任何同步的`GameplayEffect`与客户端使用相同`Prediction Key`应用的`GameplayEffect`相匹配, 那么其就是正确预测的. 目标上暂时会有`GameplayEffect`的两份拷贝直到客户端移除它预测的那一个.
* 客户端从服务端上接收回`Prediction Key`, 这是同步的`Prediction Key`, 该`Prediction Key`现在被标记为陈旧(Stale).
* 客户端移除所有由同步的陈旧(Stale)`Prediction Key`创建的`GameplayEffect`. 由服务端同步的`GameplayEffect`会持续存在. 任何客户端添加的和没有从服务端接收到一个匹配的同步版本的都被视为错误预测.

在源于`Activation Prediction Key`激活的`GameplayAbility`中的一个`instruction "window"`原子(Atomic)组期间, `Prediction Key`是保证有效的, 你可以理解为`Prediction Key`只在一帧期间是有效的. 任何潜在行为`AbilityTask`的回调函数将不再拥有一个有效的`Prediction Key`, 除非该`AbilityTask`有内建的可以生成新[Scoped Prediction Window](#concepts-p-windows)的同步点(Sync Point).  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-p-windows"></a>
#### 4.10.2 在Ability中创建新的预测窗口(Prediction Window)

为了在`AbilityTask`的回调函数中预测更多的行为, 我们需要使用新的`Scoped Prediction Key`创建`Scoped Prediction Window`, 有时这被视为客户端和服务端间的同步点(Sync Point). 一些`AbilityTask`, 像所有输入相关的`AbilityTask`, 带有创建新`Scoped Prediction Window`的内建功能, 意味着`AbilityTask`回调函数中的原子(Atomic)代码有一个有效的`Scoped Prediction Key`可供使用. 像`WaitDelay`的其他Task没有创建新`Scoped Prediction Window`以用于回调函数的内建代码, 如果你需要在`WaitDelay`这样的`AbilityTask`后预测行为, 就必须使用`OnlyServerWait`选项的`WaitNetSync AbilityTask`手动来做, 当客户端触发`OnlyServerWait`选项的`WaitNetSync`时, 它会生成一个新的基于`GameplayAbility`的`Activation Prediction Key`的`Scoped Prediction Key`, RPC其到服务端, 并将其添加到所有新应用的`GameplayEffect`. 当服务端触发`OnlyServerWait`选项的`WaitNetSync`时, 它会在继续前等待直到接收到客户端新的`Scoped Prediction Key`, 该`Scoped Prediction Key`会执行和`Activation Prediction Key`同样的操作 —— 应用到`GameplayEffect`并同步回客户端标记为陈旧(Stale). `Scoped Prediction Key`直到出域前都有效, 也就表示`Scoped Prediction Window`已经关闭了. 所以只有原子(Atomic)操作, nothing latent, 可以使用新的`Scoped Prediction Key`.  

你可以根据需求创建任意数量的`Scoped Prediction Window`.  

如果你想添加同步点(Sync Point)功能到自己的自定义`AbilityTask`, 请查看那些输入`AbilityTask`是如何从根本上注入`WaitNetSync AbilityTask`代码到自身的.  

**Note:** 当使用`WaitNetSync`时, 会阻塞服务端`GameplayAbility`继续执行直到其接收到客户端的消息. 这可能会被破解游戏的恶意用户滥用以故意延迟发送新的`Scoped Prediction Key`, 尽管Epic很少使用`WaitNetSync`, 但如果你对此担忧的话, 其建议创建一个带有延迟的新`AbilityTask`, 它会自动继续运行而无需等待客户端消息.  

样例项目在奔跑`GameplayAbility`中使用了`WaitNetSync`以在每次应用耐力花费时创建新的`Scoped Prediction Window`, 这样我们就可以进行预测. 理想上当应用花费和冷却时间时我们就想要一个有效的`Prediction Key`.  

如果你有一个在所属(Owning)客户端执行两次的预测`GameplayEffect`, 那么你的`Prediction Key`就是陈旧(Stall)的, 并且正在经历"redo"问题. 你通常可以在应用`GameplayEffect`之前将`OnlyServerWait`选项的`WaitNetSync AbilityTask`放到正确的位置以创建新的`Scoped Prediction Key`来解决这个问题.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-p-spawn"></a>
#### 4.10.3 预测性地生成Actor

在客户端预测性地生成Actor是一项高级技术. GAS对此没有提供开箱即用的功能(`SpawnActor AbilityTask`只在服务端生成Actor). 其关键是在客户端和服务端都生成同步的Actor.  

如果Actor只是用于场景装饰或者不服务于任何游戏逻辑, 简单的解决方案就是重写Actor的`IsNetRelevantFor()`函数以限制服务端同步到所属(Owning)客户端, 所属(Owning)客户端会拥有其本地生成的版本, 而服务端和其他客户端会拥有服务端同步的版本.  

```c++
bool APAReplicatedActorExceptOwner::IsNetRelevantFor(const AActor * RealViewer, const AActor * ViewTarget, const FVector & SrcLocation) const
{
	return !IsOwnedBy(ViewTarget);
}
```

如果生成的Actor影响了游戏逻辑, 像投掷物就需要预测伤害值, 那么你需要本文档范围之外的高级知识, 在Epic Games的Github上查看UnrealTournament是如何生成投掷物的, 它有一个只在所属(Owning)客户端生成且与服务端同步的投掷物.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-p-future"></a>
#### 4.10.4 GAS中预测的未来

`GameplayPrediction.h`说明了在未来可能会增加预测`GameplayEffect`移除和周期`GameplayEffect`的功能.  

来自Epic的Dave Ratti已经[表达过对其修复的兴趣](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89), 包括预测冷却时间时的延迟问题和高延迟玩家对低延迟玩家的劣势.  

来自Epic之手的新[网络预测插件(Network Prediction plugin)](#concepts-p-npp)期望能与GAS充分交互, 就像在次之前的`CharacterMovementComponent`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-p-npp"></a>
#### 4.10.5 网络预测插件(Network Prediction plugin)

Epic最近发起了一项倡议, 将使用新的网络预测插件替换`CharacterMovementComponent`, 该插件仍处于起步阶段, 但是在Unreal Engine Github上已经可以访问了, 现在说未来哪个引擎版本将首次搭载其试验版还为时尚早.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-targeting"></a>
### 4.11 Targeting

<a name="concepts-targeting-data"></a>
#### 4.11.1 Target Data

[FGameplayAbilityTargetData](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetData/index.html)是用于通过网络传输定位数据的通用结构体. `TargetData`一般用于保存AActor/UObject引用, FHitResult和其他通用的Location/Direction/Origin信息. 然而, 本质上你可以继承它以增添想要的任何数据, 其可以简单理解为在[客户端和服务端的`GameplayAbility`中传递数据](#concepts-ga-data). 基础结构体`FGameplayAbilityTargetData`不能直接使用, 而是要继承它. GAS的`GameplayAbilityTargetTypes.h`中有一些开箱即用的派生`FGameplayAbilityTargetData`结构体.  

`TargetData`一般由[Target Actor](#concepts-targeting-actors)或者手动创建, 由[AbilityTask](#concepts-at)和[GameplayEffect](#concepts-ge)通过[EffectContext](#concepts-ge-context)使用. 因为其位于`EffectContext`中, 所以[Execution](#concepts-ge-ec), [MMC](#concepts-ge-mmc), [GameplayCue](#concepts-gc)和[AttributeSet](#concepts-as)的后端函数可以访问该`TargetData`.  

我们一般不直接传递`FGameplayAbilityTargetData`, 相反我们使用[FGameplayAbilityTargetDataHandle](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FGameplayAbilityTargetDataHandle/index.html), 其内有一个`FGameplayAbilityTargetData`指针的TArray, 这个中间结构体可以为`TargetData`的多态性提供支持.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-targeting-actors"></a>
#### 4.11.2 Target Actor

`GameplayAbility`使用`WaitTargetData AbilityTask`生成[TargetActor](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor/index.html)以在世界中可视化和获取定位信息. `TargetActor`可以选择使用[GameplayAbilityWorldReticles](#concepts-targeting-reticles)显示当前目标. 经确认后, 定位信息将作为[TargetData](#concepts-targeting-data)返回, 之后其可以传递给`GameplayEffect`.  

`TargetActor`是基于`AActor`的, 因此它可以使用任意种类的可视组件来表示其是在何处和如何定位的, 例如静态网格物(Static Mesh)和贴花(Decal). 静态网格物(Static Mesh)可以用来可视化角色将要建造的物体, 贴花(Decal)可以用来表现地面上的效果区域. 样例项目使用带有地面贴花的[AGameplayAbilityTargetActor_GroundTrace](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityTargetActor_Grou-/index.html)表示陨石技能的伤害区域效果. 它们也可以什么都不显示, 例如, GASShooter中的枪支命中判断, 要对其目标的射线检测显示些什么就是没有意义的.  

其使用基本的射线或者碰撞(Overlap)捕获定位信息, 并根据`TargetActor`的实现将`FHitResult`或`AActor`数组转换为`TargetData`. `WaitTargetData AbilityTask`通过`TEnumAsByte<EGameplayTargetingConfirmation::Type> ConfirmationType`参数决定目标何时被确认, 当不使用`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`时, `TargetActor`一般就在`Tick()`中执行射线/Overlap, 并根据它的实现来更新位置信息到`FHitResult`. 尽管是在`Tick()`中执行的射线/Overlap, 但是一般不用担心, 因为它是不同步的并且一般没有多个(尽管存在多个)`TargetActor`同时运行, 只是要留意它使用的是`Tick()`, 一些复杂的`TargetActor`可能会在其中做很多事情, 就像GASShooter中火箭筒的二技能. 当`Tick()`中的检测对客户端响应非常频繁时, 如果性能影响很大的话, 你可能就要考虑降低`TargetActor`的Tick频率. 对于`TEnumAsByte<EGameplayTargetingConfirmation::Type::Instant`, `TargetActor`会立即生成, 产生`TargetData`, 然后销毁, 并且从不会调用`Tick()`.  

|EGameplayTargetingConfirmation::Type|何时确认Target|
|:-:|:-:|
|Instant|该定位无需特殊逻辑即可立即进行, 或者用户输入决定何时开始.|
|UserConfirmed|当[Ability绑定到`Confirm`输入](#concepts-ga-input)且用户确认或调用`UAbilitySystemComponent::TargetConfirm()`时触发该定位. `TargetActor`也会响应绑定的`Cancel`输入或者调用`UAbilitySystemComponent::TargetCancel()`来取消定位.|
|Custom|GameplayTargeting Ability负责调用`UGameplayAbility::ConfirmTaskByInstanceName()`来决定何时准备好定位数据. `TargetActor`也可以响应`UGameplayAbility::CancelTaskByInstanceName()`来取消定位.|
|CustomMulti|GameplayTargeting Ability负责调用`UGameplayAbility::ConfirmTaskByInstanceName()`来决定何时准备好定位数据. `TargetActor`也可以响应`UGameplayAbility::CancelTaskByInstanceName()`来取消定位. 不应在数据产生时结束AbilityTask.|

并不是所有的`TargetActor`都支持每个`EGameplayTargetingConfirmation::Type`, 例如, `AGameplayAbilityTargetActor_GroundTrace`就不支持`Instant`确认.  

`WaitTargetData AbilityTask`将一个`AGameplayAbilityTargetActor`类作为参数, 其会在每次`AbilityTask`激活时生成一个实例并且在该`AbilityTask`结束时销毁`TargetActor`. `WaitTargetDataUsingActor AbilityTask`接受一个已经生成的`TargetActor`, 但是在该`AbilityTask`结束时仍会销毁它. 这两种`AbilityTask`都是低效的, 因为它们在每次使用时都要生成或需要一个新生成的`TargetActor`, 它们用于原型开发是很好的, 但是在实际发布版本中, 如果有持续产生`TargetData`的场景, 像全自动开火, 你可能就要探索优化它的办法. GASShooter有一个自定义的[AGameplayAbilityTargetActor](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/GSGATA_Trace.h)子类和一个完全重写的[WaitTargetDataWithReusableActor AbilityTask](https://github.com/tranek/GASShooter/blob/master/Source/GASShooter/Public/Characters/Abilities/AbilityTasks/GSAT_WaitTargetDataUsingActor.h), 其允许你复用`TargetActor`而无需将其销毁.  

`TargetActor`默认是不可同步的. 然而, 如果在你的游戏中向其他玩家展示本地玩家正在定位的目标是有意义的, 那么它也可以被设计成可同步的, 它也确实包含在`WaitTargetData AbilityTask`中通过RPC和服务端通信的默认功能. 如果`TargetActor`的`ShouldProduceTargetDataOnServer`属性设置为false, 那么客户端就会在确认时通过`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`中的`CallServerSetReplicatedTargetData()`RPC它的`TargetData`到服务端. 如果`ShouldProduceTargetDataOnServer`为true, 那么客户端就会发送一个通用确认事件, `EAbilityGenericReplicatedEvent::GenericConfirm`, 在`UAbilityTask_WaitTargetData::OnTargetDataReadyCallback()`中RPC到服务端, 服务端在接收到RPC并产生数据时就会执行射线或者Overlap检测. 如果客户端取消该定位, 它会发送一个通用取消事件, `EAbilityGenericReplicatedEvent::GenericCancel`, 在`UAbilityTask_WaitTargetData::OnTargetDataCancelledCallback`中RPC到服务端. 你可以看到, 在`TargetActor`和`WaitTargetData AbilityTask`中存在大量委托, `TargetActor`响应输入来产生广播`TargetData`的准备, 确认或者取消委托, `WaitTargetData`监听`TargetActor`的`TargetData`的准备, 确认和取消委托, 并将该信息转发回`GameplayAbility`和服务端. 如果向服务端发送`TargetData`, 为了防止作弊, 可能需要在服务端做校验以保证该`TargetData`是合理的. 直接在服务端上产生`TargetData`可以完全避免这个问题, 但是可能会导致所属(Owning)客户端的错误预测.  

根据使用的不同`AGameplayAbilityTargetActor`子类, `WaitTargetData AbilityTask`节点会暴露不同的`ExposeOnSpawn`参数, 一些常用的参数包括:  

|常用`TargetActor`参数|定义|
|:-:|:-:|
|Debug|如果为真, 每当非发行版本中的`TargetActor`执行射线检测时, 其会绘制debug射线/Overlap信息. 请记住, `non-Instant TargetActor`会在`Tick()`中执行射线检测, 因此这些debug绘制调用也会在`Tick()`中触发.|
|Filter|[可选]当射线/Overlap触发时, 用于从Target中过滤(移除)Actor的特殊结构体. 典型的使用案例是过滤玩家的`Pawn`, 其要求Target是特殊类. 查看[Target Data Filters](#concepts-target-data-filters)以获得更多高级使用案例.|
|Reticle Class|[可选]`TargetActor`生成的`AGameplayAbilityWorldReticle`子类.|
|Reticle Parameters|[可选]配置你的Reticle. 查看[Reticles](#concepts-targeting-reticles).|
|Start Location|用于设置射线检测应该从何处开始的特殊结构体. 一般这应该是玩家的视口(Viewport), 枪口或者Pawn的位置.|

使用默认的`TargetActor`类时, Actor只有直接在射线/Overlap中时才是有效的, 如果它离开射线/Overlap(它移动开或你的视线转向别处), 就不再有效了. 如果你想`TargetActor`记住最后有效的Target, 就需要添加这项功能到一个自定义的`TargetActor`类. 我称之为持久化Target, 因为其会持续存在直到`TargetActor`接收到确认或取消消息, `TargetActor`会在它的射线/Overlap中找到一个新的有效Target, 或者Target不再有效(已销毁). GASShooter对火箭筒二技能的制导火箭定位使用了持久化Target.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-target-data-filters"></a>
#### 4.11.3 TargetData过滤器

同时使用`Make GameplayTargetDataFilter`和`Make Filter Handle`节点, 你可以过滤玩家的`Pawn`或者只选择某个特定类. 如果需要更多高级过滤条件, 可以继承`FGameplayTargetDataFilter`并重写`FilterPassesForActor`函数.  

```c++
USTRUCT(BlueprintType)
struct GASDOCUMENTATION_API FGDNameTargetDataFilter : public FGameplayTargetDataFilter
{
	GENERATED_BODY()

	/** Returns true if the actor passes the filter and will be targeted */
	virtual bool FilterPassesForActor(const AActor* ActorToBeFiltered) const override;
};
However, this will not work directly into the Wait Target Data node as it requires a FGameplayTargetDataFilterHandle. A new custom Make Filter Handle must be made to accept the subclass:

FGameplayTargetDataFilterHandle UGDTargetDataFilterBlueprintLibrary::MakeGDNameFilterHandle(FGDNameTargetDataFilter Filter, AActor* FilterActor)
{
	FGameplayTargetDataFilter* NewFilter = new FGDNameTargetDataFilter(Filter);
	NewFilter->InitializeFilterContext(FilterActor);

	FGameplayTargetDataFilterHandle FilterHandle;
	FilterHandle.Filter = TSharedPtr<FGameplayTargetDataFilter>(NewFilter);
	return FilterHandle;
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-targeting-reticles"></a>
#### 4.11.4 Gameplay Ability World Reticles

当使用已确认的`non-Instant` [TargetActor](#concepts-targeting-actors)定位时, [AGameplayAbilityWorldReticles(Reticles)](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/AGameplayAbilityWorldReticle/index.html)可以可视化正在定位的目标. `TargetActor`负责所有`Reticle`生命周期的生成和销毁. `Reticle`是AActor, 因此其可以使用任意种类的可视组件作为表现形式. GASShooter中常见的一种实现方式是使用`WidgetComponent`在屏幕空间中显示UMG Widget(永远面对玩家的摄像机). `Reticle`不知道其正在定位的Actor, 但是你可以通过继承在自定义`TargetActor`中实现该功能. `TargetActor`一般在每次`Tick()`中更新`Reticle`的位置为Target的位置.  

GASShooter对火箭筒二技能制导火箭锁定的目标使用了`Reticle`. 敌人身上的红色标识就是`Reticle`, 相似的白色图像是火箭筒的准星.  

![Reticles in GASShooter](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gameplayabilityworldreticle.png)  

`Reticle`带有一些面向设计师的`BlueprintImplementableEvents`(它们被设计用来在蓝图中开发):  

```c++
/** Called whenever bIsTargetValid changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnValidTargetChanged(bool bNewValue);

/** Called whenever bIsTargetAnActor changes value. */
UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnTargetingAnActor(bool bNewValue);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void OnParametersInitialized();

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamFloat(FName ParamName, float value);

UFUNCTION(BlueprintImplementableEvent, Category = Reticle)
void SetReticleMaterialParamVector(FName ParamName, FVector value);
```

`Reticle`可以选择使用`TargetActor`提供的[FWorldReticleParameters](https://docs.unrealengine.com/en-US/API/Plugins/GameplayAbilities/Abilities/FWorldReticleParameters/index.html)来配置, 默认结构体只提供一个变量`FVector AOEScale`, 尽管你可以在技术上继承该结构体, 但是`TargetActor`只接受基类结构体, 不允许在默认`TargetActor`中子类化该结构体似乎有些短见, 然而, 如果你创建了自己的自定义`TargetActor`, 就可以提供自定义的`Reticle`参数结构体并在生成`Reticle`时手动传递它到`AGameplayAbilityWorldReticles`子类.  

`Reticle`默认是不可同步的, 但是如果在你的游戏中向其他玩家展示本地玩家正在定位的目标是有意义的, 那么它也可以被设计成可同步的.  

`Reticle`只会显示在默认`TargetActor`的当前有效Target上, 例如, 如果你正在使用`AGameplayAbilityTargetActor_SingleLineTrace`对目标执行射线检测, 敌人只有直接处于射线路径上时`Reticle`才会显示, 如果角色视线看向别处, 那么该敌人就不再是一个有效Target, 因此该`Reticle`就会消失. 如果你想`Reticle`保留在最后一个有效Target上, 就需要自定义`TargetActor`来记住最后一个有效Target并使`Reticle`保留在其上. 我称之为持久化Target, 因为其会持续存在直到`TargetActor`接收到确认或取消消息, `TargetActor`会在它的射线/Overlap中找到一个新的有效Target, 或者Target不再有效(已销毁). GASShooter对火箭筒二技能的制导火箭定位使用了持久化Target.  

**[⬆ 返回目录](#table-of-contents)**

<a name="concepts-targeting-containers"></a>
#### 4.11.5 Gameplay Effect Containers Targeting

[GameplayEffectContainer](#concepts-ge-containers)提供了一个可选的产生[TargetData](#concepts-targeting-data)的高效方法. 当`EffectContainer`在客户端和服务端上应用时, 该定位会立即进行, 它比[TargetActor](#concepts-targeting-actors)更有效率, 因为它是运行在定位对象的CDO(Class Default Object)上的(没有Actor的生成和销毁), 但是它不支持用户输入, 无需确认即可立即进行, 不能取消, 并且不能从客户端向服务端发送数据(在两者上产生数据), 它对即时射线检测和碰撞Overlap很有用. Epic的[Action RPG Sample Project](https://www.unrealengine.com/marketplace/en-US/slug/action-rpg)包含两种使用Container定位的样例 —— 定位Ability拥有者和从事件拉取`TargetData`, 它还在蓝图中实现了在距玩家某个偏移处(由蓝图子类设置)做球形射线检测(Sphere Trace), 你可以在C++或蓝图中继承`URPGTargetType`以实现自己的定位类型.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae"></a>
## 5. 常用的Abilty和Effect

<a name="cae-stun"></a>
### 5.1 眩晕(Stun)

一般在眩晕状态时, 我们就要取消Character所有已激活的`GameplayAbility`, 阻止新的`GameplayAbility`激活, 并在整个眩晕期间阻止移动. 样例项目的陨石`GameplayAbility`会在击中的目标上应用眩晕效果.  

为了取消目标活跃的`GameplayAbility`, 可以在眩晕[`GameplayTag`添加](#concepts-gt-change)时调用`AbilitySystemComponent->CancelAbilities()`.  

为了在眩晕时阻止新的`GameplayAbility`激活, 可以在`GameplayAbility`的[Activation Blocked Tags GameplayTagContainer](#concepts-ga-tags)中添加眩晕`GameplayTag`.  

为了在眩晕时阻止移动, 我们可以在拥有者拥有眩晕`GameplayTag`时重写`CharacterMovementComponent`的`GetMaxSpeed()`函数以返回0.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-sprint"></a>
### 5.2 奔跑(Sprint)

样例项目提供了一个如何奔跑的例子 —— 按住左Shift时跑得更快.  

更快的移动由`CharacterMovementComponent`通过网络发送flag到服务端预测性地处理. 详见`GDCharacterMovementComponent.h/cpp`.  

`GA`处理响应左Shift输入, 告知`CMC`开始和停止奔跑, 并且在左Shift按下时预测性地消耗耐力. 详见`GA_Sprint_BP`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-ads"></a>
### 5.3 瞄准(Aim Down Sight)

样例项目处理它和奔跑时完全一样, 除了降低移动速度而不是提高它.  

详见`GDCharacterMovementComponent.h/cpp`是如何预测性地降低移动速度的.  

详见`GA_AimDownSight_BP`是如何处理输入的. 瞄准时是不消耗耐力的.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-ls"></a>
### 5.4 生命偷取(Lifesteal)

我在伤害[ExecutionCalculation](#concepts-ge-ec)中处理生命偷取. `GameplayEffect`会有一个像`Effect.CanLifesteal`样的`GameplayTag`, `ExecutionCalculation`会检查`GameplayEffectSpec`是否有`Effect.CanLifesteal GameplayTag`, 如果该`GameplayTag`存在, `ExecutionCalculation`会使用作为Modifer的生命值[创建一个动态的`即刻(Instant)GameplayEffect`](#concepts-ge-dynamic), 并将其应用回源(Source)ASC.  

```c++
if (SpecAssetTags.HasTag(FGameplayTag::RequestGameplayTag(FName("Effect.Damage.CanLifesteal"))))
{
	float Lifesteal = Damage * LifestealPercent;

	UGameplayEffect* GELifesteal = NewObject<UGameplayEffect>(GetTransientPackage(), FName(TEXT("Lifesteal")));
	GELifesteal->DurationPolicy = EGameplayEffectDurationType::Instant;

	int32 Idx = GELifesteal->Modifiers.Num();
	GELifesteal->Modifiers.SetNum(Idx + 1);
	FGameplayModifierInfo& Info = GELifesteal->Modifiers[Idx];
	Info.ModifierMagnitude = FScalableFloat(Lifesteal);
	Info.ModifierOp = EGameplayModOp::Additive;
	Info.Attribute = UPAAttributeSetBase::GetHealthAttribute();

	SourceAbilitySystemComponent->ApplyGameplayEffectToSelf(GELifesteal, 1.0f, SourceAbilitySystemComponent->MakeEffectContext());
}
```

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-random"></a>
### 5.5 在客户端和服务端中生成随机数

有时你需要在`GameplayAbility`中生成随机数用于枪支后坐力或者子弹扩散, 客户端和服务端都需要生成相同的随机数, 要做到这一点, 我们必须在`GameplayAbility`激活时设置相同的`随机数种子`, 你需要在每次激活`GameplayAbility`时设置`随机数种子`, 以防客户端错误预测激活或者它的随机数列表与服务端的不同步.  

|随机种子设置方法|描述|
|:-:|:-:|
|使用`Activation Prediction Key`|`GameplayAbility`的`Activation Prediction Key`是一个确保同步并且在客户端和服务端的`Activation()`中都可用的int16类型值. 你可以在客户端和服务端中设置其作为`随机数种子`. 该方法的缺点是每次游戏开始时`Prediction Key`总是从0开始并且会在生成key之后持续增加, 这意味着每场游戏都有着及其相同的随机数序列, 这可能满足或不满足你的随机数需要.|
|激活`GameplayAbility`时通过事件负载(Event Payload)发送种子|通过事件激活`GameplayAbility`并通过可同步的事件负载(Event Payload)从客户端发送随机生成的种子到服务端, 这允许更高的随机性, 但是客户端也容易被破解而每次只发送相同的种子. 通过事件激活`GameplayAbility`也会阻止其从用户绑定激活.|

如果你的随机偏差很小, 大多数玩家是不会注意到每次游戏的随机序列都是相同的, 那么使用`Activation Prediction Key`作为随机种子就应该适用于你. 如果你正在做一些更复杂的事, 需要防范破解者, 那么使用`Server Initiated GameplayAbility`会更好, 服务端可以创建`Prediction Key`或者生成随机数种子来通过事件负载(Event Payload)发送.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-crit"></a>
### 5.6 暴击(Critical Hits)

我在伤害[ExecutionCalculation](#concepts-ge-ec)中处理暴击. `GameplayEffect`会有一个像`Effect.CanCrit`样的`GameplayTag`, `ExecutionCalculation`会检查`GameplayEffectSpec`是否有`Effect.CanCrit  GameplayTag`, 如果该`GameplayTag`存在, `ExecutionCalculation`就会生成一个与暴击率相关联的随机数(从Source捕获的`Attribute`), 如果成功的话就会增加暴击伤害(另一个从Source捕获的`Attribute`). 因为我没有预测伤害, 所以不必担心在客户端和服务端上同步随机数生成器的问题(`ExecutionCalculation`只运行在服务端上). 如果你尝试使用`MMC`预测性地执行伤害计算, 就必须从`GameplayEffectSpec->GameplayEffectContext->GameplayAbilityInstance`中获取随机数种子的引用.  

查看GASShooter是如何处理爆头问题的, 其概念是一样的, 除了不再依赖随机数作为概率而是检测`FHitResult`骨骼名.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-nonstackingge"></a>
### 5.7 非堆栈GameplayEffect, 但是只有其最高级(Greatest Magnitude)才能实际影响Target

Paragon中的Slow Effect是非堆栈的. 应用每个实例并且像平常一样跟踪其生命周期, 但是只有最高级(Greatest Magnitude)的Slow Effect才能实际影响`Character`. GAS为这种场景提供了开箱即用的`AggregatorEvaluateMetaData`, 详见[AggregatorEvaluateMetaData()](#concepts-as-onattributeaggregatorcreated)及其实现.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-paused"></a>
### 5.8 游戏暂停时生成`TargetData`

如果你需要在等待玩家从`WaitTargetData AbilityTask`生成[TargetData](#concepts-targeting-data)时暂停游戏, 我建议使用`slomo 0`而不是暂停.  

**[⬆ 返回目录](#table-of-contents)**

<a name="cae-onebuttoninteractionsystem"></a>
### 5.9 按钮交互系统(Button Interaction System)

GASShooter实现了一个按钮交互系统, 玩家可以按下或按住'E'键来和可交互对象交互, 像复活玩家, 打开武器箱, 打开或关闭滑动门.  

**[⬆ 返回目录](#table-of-contents)**

<a name="debugging"></a>
## 6. 调试GAS

通常在调试GAS相关的问题时, 你感兴趣的事情像:  

> "我的Attribute的值是多少?"
> "我有哪些GameplayTag?"
> "我现在有哪些GameplayEffect?"
> "我已经授予的Ability有哪些, 哪个正在运行, 哪个被堵止激活?"

GAS有两种技术可以在运行时解决这些问题 —— [showdebug abilitysystem](#debugging-sd)和在[GameplayDebugger](#debugging-gd)中Hook.  

**Tip:** UE4倾向于优化C++代码, 这使得某些函数变得很难调试, 当深入追踪代码时很少遇到这种情况. 如果将Visual Studio的解决方案配置设置为`DebugGame Editor`仍然不能追踪代码或者监视变量, 可以使用`PRAGMA_DISABLE_OPTIMIZATION_ACTUAL`和`PRAGMA_ENABLE_OPTIMIZATION_ACTUAL`宏包裹优化函数来关闭优化, 这不能在插件代码中使用除非从源码重新编译插件. 这可以或不可以用于inline函数, 取决于它的作用和位置. 确保完成调试后移除这两个宏!  

```c++
PRAGMA_DISABLE_OPTIMIZATION_ACTUAL
void MyClass::MyFunction(int32 MyIntParameter)
{
	// My code
}
PRAGMA_ENABLE_OPTIMIZATION_ACTUAL
```

**[⬆ 返回目录](#table-of-contents)**

<a name="debugging-sd"></a>
### 6.1 showdebug abilitysystem

在游戏中的控制台输入`showdebug abilitysystem`. 该特性被分为三"页", 三页都会显示当前拥有的`GameplayTag`, 在控制台输入`AbilitySystem.Debug.NextCategory`来换页.  

第一页显示了所有`Attribute`的`CurrentValue`: ![First Page of showdebug abilitysystem](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/showdebugpage1.png)  

第二页显示了所有应用到你的`持续(Duration)`和`无限(Infinite)GameplayEffect`, 它们的堆栈数, 使用的`GameplayTag`和`Modifier`. ![Second Page of showdebug abilitysystem](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/showdebugpage2.png)  

第三页显示了所有授予到你的`GameplayAbility`, 无论其是否正在运行, 无论其是否被阻止激活, 和当前正在运行的`AbilityTask`的状态.  ![Third Page of showdebug abilitysystem](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/showdebugpage3.png)  

你可以使用`PageUp`和`PageDown`切换Target, 页面只显示你本地控制的`Character`中的`ASC`数据, 然而, 使用`AbilitySystem.Debug.NextTarget`和`AbilitySystem.Debug.PrevTarget`可以显示其他`ASC`的数据, 但是不会显示调试信息的上半部分, 也不会更新绿色目标长方体, 因此无法知道当前定位的是哪个`ASC`, 该BUG已经被提交到[https://issues.unrealengine.com/issue/UE-90437.](https://issues.unrealengine.com/issue/UE-90437).  

**Note:**  为了`showdebug abilitysystem`可以使用, 必须在GameMode中选择一个实际的HUD类, 否则就会找不到该命令并返回"Unknown Command".  

**[⬆ 返回目录](#table-of-contents)**

<a name="debugging-gd"></a>
### 6.2 Gameplay Debugger

GAS向Gameplay Debugger中添加了功能, 使用``反引号(`)``键以访问Gameplay Debugger. 按下小键盘的3键以启用Ability分类, 取决于你所拥有的插件, 分类可能是不同的. 如果你的键盘没有小键盘, 比如笔记本, 那么你可以在项目设置(Project Settings)里修改键盘绑定.  

当你想要查看其他Character的`GameplayTag`, `GameplayEffect`和`GameplayAbility`时可以使用Gameplay Debugger, 可惜的是它不能显示Target的`Attribute`中的`CurrentValue`. 它会定位屏幕中央的任何Character, 你可以通过选择编辑器世界大纲(World Outliner)或者看向另一个不同的Character并再次按下``反引号(`)``键来修改Target. 当前监视的Character上方有最大的红色圆.  

**[⬆ 返回目录](#table-of-contents)**

<a name="debugging-log"></a>
### 6.3 GAS日志(Logging)

GAS包含了大量不同详细级别的日志生成语句, 你很可能见到的是`ABILITY_LOG()`这样的语句. 默认的详细级别是`Display`, 更高的默认不会显示在控制台里.  

为了修改某个日志分类的详细级别, 在控制台中输入:  

```c++
log [category] [verbosity]
```

例如, 为了开启`ABILITY_LOG()`语句, 应该在控制台中输入:  

```c++
log LogAbilitySystem VeryVerbose
```

为了恢复默认, 输入:  

```c++
log LogAbilitySystem Display
```

为了显示所有的日志分类, 输入:  

```c++
log list
```

值得注意的GAS相关的日志分类:  

|日志分类|默认详细级别|
|:-:|:-:|
|LogAbilitySystem|Display|
|LogAbilitySystemComponent|Log|
|LogGameplayCueDetails|Log|
|LogGameplayCueTranslator|Display|
|LogGameplayEffectDetails|Log|
|LogGameplayEffects|Display|
|LogGameplayTags|Log|
|LogGameplayTasks|Log|
|VLogAbilitySystem|Display|

详情查看[日志Wiki](https://www.ue4community.wiki/Legacy/Logs,_Printing_Messages_To_Yourself_During_Runtime).  

**[⬆ 返回目录](#table-of-contents)**

<a name="optimizations"></a>
## 7. 优化

<a name="optimizations-abilitybatching"></a>
## 7.1 Ability批处理

[GameplayAbility](#concepts-ga)在一帧中的的激活、选择性地向服务器发送`TargetData`、结束可以被[批处理而将2-3个RPC压缩成一个RPC](#concepts-ga-batching). 这种RPC常用于枪支的命中判断.  

**[⬆ 返回目录](#table-of-contents)**

<a name="optimizations-gameplaycuebatching"></a>
## 7.2 GameplayCue批处理

如果你需要同时发送很多[GameplayCue](#concepts-gc), 可以考虑将其[批处理成一个RPC](#concepts-gc-batching), 目标就是减少RPC数量(`GameplayCue`是不可靠的网络多播(NetMulticast))并以尽可能少的数据量发送.  

**[⬆ 返回目录](#table-of-contents)**

<a name="optimizations-ascreplicationmode"></a>
## 7.3 AbilitySystemComponent同步模式(Replication Mode)

默认情况下, [ASC](#concepts-asc)处于[Full Replication](#concepts-asc-rm)模式, 这会同步所有的[GameplayEffect](#concepts-ge)到每个客户端(对单人游戏来说很好). 在多人游戏中, 设置玩家拥有的`ASC`为`Mixed Replication`模式, AI控制的Character为`Minimal Replication`模式, 这会将应用到玩家Character上的`GE`仅同步到该Character的拥有者, 应用到AI控制的Character上的`GE`永远不会同步到客户端. 无论是什么同步模式, [GameplayTag](#concepts-gt)仍会进行同步, [GameplayCue](#concepts-gc)仍会以不可靠的网络多播(NetMulticast)到所有客户端. 当所有客户端都不需要查看这些数据时, 这会减少从`GE`同步的网络数据.  

**[⬆ 返回目录](#table-of-contents)**

<a name="optimizations-attributeproxyreplication"></a>
## 7.4 Attribute代理同步

在有很多玩家的大型游戏中, 像Fortnite大逃杀(Fortnite Battle Royale), 有大量存在于常相关`PlayerState`上的[ASC](#concepts-asc), 其会同步大量[Attribute](#concepts-a). 为了优化该瓶颈, Fortnite禁止在`PlayerState::ReplicateSubobjects()`中完全同步`ASC`和它的[AttributeSet](#concepts-as)到模拟玩家控制的代理(Simulated Player-Controlled Proxy)上. Autonomou代理和AI控制的`Pawn`仍然根据其[同步模式](#concepts-asc-rm)来完全同步. Fortnite使用了玩家Pawn上的可同步代理结构体, 而不是同步常相关`PlayerState`上的`ASC`的`Attribute`. 当服务端ASC上的`Attribute`改变时, 其也会在代理结构体中改变, 客户端会从代理结构体接收同步的`Attribute`并将修改返回其本地`ASC`, 这允许`Attribute`同步使用`Pawn`的相关性(Relevancy)和`NetUpdateFrequency`, 该代理结构体也会使用位掩码(Bitmask)同步一个小的`GameplayTag`白名单集合. 该优化减少了网络数据量, 并允许我们利用Pawn相关性(Relevancy). AI控制的`Pawn`的`ASC`位于已经使用其相关性的`Pawn`上, 因此其并不需要该优化.  

> I’m not sure if it is still necessary with other server side optimizations that have been done since then (Replication Graph, etc) and it is not the most maintainable pattern.

来自Epic的Dave Ratti回答[社区问题#3](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89).  

**[⬆ 返回目录](#table-of-contents)**

<a name="optimizations-asclazyloading"></a>
## 7.5 ASC懒加载

Fortnite大逃杀(Fortnite Battle Royale)世界中有很多可损坏的`AActor`(树木, 建筑物等等), 每个都有一个`ASC`, 这会增加内存消耗, Fortnite通过只在需要时(当其第一次被玩家伤害时)懒加载`ASC`的方式优化该问题, 这会减少整体内存消耗, 因为某些`AActor`在一局比赛中可能永远不会被伤害.  

**[⬆ 返回目录](#table-of-contents)**

<a name="qol"></a>
# 8. Quality of Life Suggestions

<a name="qol-gameplayeffectcontainers"></a>
## 8.1 Gameplay Effect Containers

[GameplayEffectContainer](#concepts-ge-containers)将[GameplayEffectSpec](#concepts-ge-spec), [TargetData](#concepts-targeting-data), 简单定位和其他相关功能整合进简单易用的结构体, 这对转移`GameplayEffectSpec`到Ability生成的抛射物很有用, 该抛射物之后会在碰撞体上应用`GameplayEffectSpec`.  

**[⬆ 返回目录](#table-of-contents)**

<a name="qol-asynctasksascdelegates"></a>
## 8.2 将蓝图AsyncTask绑定到ASC委托

为了增加设计师友好的迭代次数, 特别是在为UI设计UMG Widget时, 可以创建蓝图AsyncTask(在C++中)以直接在UMG蓝图图表中绑定到`ASC`中常见的修改委托, 唯一的告诫就是其必须手动销毁(比如在widget销毁时), 否则就会永远存于内存中.样例项目包含三个蓝图AsyncTask.  

监听`Attribute`修改:  

![Listen for Attributes Changes BP Node](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/attributeschange.png)  

监听冷却时间修改:  

![Listen for Cooldown Change BP Node](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/cooldownchange.png)  

监听`GE`堆栈修改:  

![Listen for GameplayEffect Stack Change BP Node](https://raw.githubusercontent.com/BillEliot/GASDocumentation_Chinese/main/Images/gestackchange.png)  

**[⬆ 返回目录](#table-of-contents)**

<a name="troubleshooting"></a>
# 9. 疑难解答

<a name="troubleshooting-notlocal"></a>
## 9.1 LogAbilitySystem: Warning: Can't activate LocalOnly or LocalPredicted ability %s when not local!

你需要在[客户端初始化`ASC`](#concepts-asc-setup).  

**[⬆ 返回目录](#table-of-contents)**

<a name="troubleshooting-scriptstructcache"></a>
## 9.2 `ScriptStructCache`错误

你需要调用[UAbilitySystemGlobals::InitGlobalData()](#concepts-asg-initglobaldata).  

**[⬆ 返回目录](#table-of-contents)**

<a name="troubleshooting-replicatinganimmontages"></a>
## 9.3 动画蒙太奇不能同步到客户端

确保在[GameplayAbility](#concepts-ga)中正在使用的是`PlayMontageAndWait`节点而不是`PlayMontage`, 该[AbilityTask](#concepts-at)可以通过`ASC`自动同步蒙太奇而`PlayMontage`节点不可以.  

**[⬆ 返回目录](#table-of-contents)**

<a name="acronyms"></a>
# 10. ASC常用术语缩略

|术语|缩略|
|:-:|:-:|
|AbilitySystemComponent|ASC|
|AbilityTask|AT|
|Action RPG Sample Project by Epic|ARPG, ARPG Sample|
|CharacterMovementComponent|CMC|
|GameplayAbility|GA|
|GameplayAbilitySystem|GAS|
|GameplayCue|GC|
|GameplayEffect|GE|
|GameplayEffectExecutionCalculation|ExecCalc, Execution|
|GameplayTag|Tag, GT|
|ModiferMagnitudeCalculation|ModMagCalc, MMC|

**[⬆ 返回目录](#table-of-contents)**

<a name="resources"></a>
# 11. 其他资源

[官方文档](https://docs.unrealengine.com/en-US/InteractiveExperiences/GameplayAbilitySystem/index.html)  

源代码! 特别是`GameplayPrediction.h`.  

[Epic的Action RPG样例项目](https://www.unrealengine.com/marketplace/en-US/slug/action-rpg)  

[来自Epic的Dave Ratti回复社区关于GAS的问题](https://epicgames.ent.box.com/s/m1egifkxv3he3u3xezb9hzbgroxyhx89)  

[Unreal Slackers Discord](https://unrealslackers.org)有一个专注于GAS`#gameplay-abilities-plugin`的文字频道  

[Dan 'Pan'的Github库](https://github.com/Pantong51/GASContent)  

[SabreDartStudios的YouTube视频](https://www.youtube.com/channel/UCCFUhQ6xQyjXDZ_d6X_H_-A)

**[⬆ 返回目录](#table-of-contents)**

<a name="changelog"></a>
# 12. GAS更新日志

这是从Unreal Engine官方升级日志和我遇到的且未记录的升级中整理的一份值得一看的升级列表, 如果你发现某些没有列在其中, 请提issue或者PR.  

**[⬆ 返回目录](#table-of-contents)**

<a name="changelog-4.26"></a>
## 4.26

* GAS plugin is no longer flagged as beta.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Added the path string arg to a message to fix a crash in UGameplayCueManager::VerifyNotifyAssetIsInValidPath.
* Crash Fix: Fixed an access violation crash in AbilitySystemComponent_Abilities when using a ptr without checking it.
* Bug Fix: Fixed a bug where stacking GEs that did not reset the duration on additional instances of the effect being applied.
* Bug Fix: Fixed an issue that caused CancelAllAbilities to only cancel non-instanced abilities.
* New: Added optional tag parameters to gameplay ability commit functions.
* New: Added StartTimeSeconds to PlayMontageAndWait ability task and improved comments.
* New: Added tag container "DynamicAbilityTags" to FGameplayAbilitySpec. These are optional ability tags that are replicated with the spec. They are also captured as source tags by applied gameplay effects.
* New: GameplayAbility IsLocallyControlled and HasAuthority functions are now callable from Blueprint.
* New: Visual logger will now only collect and store info about instant GEs if we're currently recording visual logging data.
* New: Added support for redirectors on gameplay attribute pins in blueprint nodes.
* New: Added new functionality for when root motion movement related ability tasks end they will return the movement component's movement mode to the movement mode it was in before the task started.

**[⬆ 返回目录](#table-of-contents)**

<a name="changelog-4.25.1"></a>
## 4.25.1

* Fixed! UE-92787 Crash saving blueprint with a Get Float Attribute node and the attribute pin is set inline
* Fixed! UE-92810 Crash spawning actor with instance editable gameplay tag property that was changed inline

**[⬆ 返回目录](#table-of-contents)**

<a name="changelog-4.25"></a>
## 4.25

* Fixed prediction of RootMotionSource AbilityTasks
* [GAMEPLAYATTRIBUTE_REPNOTIFY()](#concepts-as-attributes) now additionally takes in the old Attribute value. We must supply that as the optional parameter to our OnRep functions. Previously, it was reading the attribute value to try to get the old value. However, if called from a replication function, the old value had already been discarded before reaching SetBaseAttributeValueFromReplication so we'd get the new value instead.
* Added [NetSecurityPolicy](#concepts-ga-netsecuritypolicy) to UGameplayAbility.
* Crash Fix: Fixed a crash when adding a gameplay tag without a valid tag source selection.
* Crash Fix: Removed a few ways for attackers to crash a server through the ability system.
* Crash Fix: We now make sure we have a GamplayEffect definition before checking tag requirements.
* Bug Fix: Fixed an issue with gameplay tag categories not applying to function parameters in Blueprints if they were part of a function terminator node.
* Bug Fix: Fixed an issue with gameplay effects' tags not being replicated with multiple viewports.
* Bug Fix: Fixed a bug where a gameplay ability spec could be invalidated by the InternalTryActivateAbility function while looping through triggered abilities.
* Bug Fix: Changed how we handle updating gameplay tags inside of tag count containers. When deferring the update of parent tags while removing gameplay tags, we will now call the change-related delegates after the parent tags have updated. This ensures that the tag table is in a consistent state when the delegates broadcast.
* Bug Fix: We now make a copy of the spawned target actor array before iterating over it inside when confirming targets because some callbacks may modify the array.
* Bug Fix: Fixed a bug where stacking GamplayEffects that did not reset the duration on additional instances of the effect being applied and with set by caller durations would only have the duration correctly set for the first instance on the stack. All other GE specs in the stack would have a duration of 1 second. Added automation tests to detect this case.
* Bug Fix: Fixed a bug that could occur if handling gameplay event delegates modified the list of gameplay event delegates.
* Bug Fix: Fixed a bug causing GiveAbilityAndActivateOnce to behave inconsistently.
* Bug Fix: Reordered some operations inside FGameplayEffectSpec::Initialize to deal with a potential ordering dependency.
* New: UGameplayAbility now has an OnRemoveAbility function. It follows the same pattern as OnGiveAbility and is only called on the primary instance of the ability or the class default object.
* New: When displaying blocked ability tags, the debug text now includes the total number of blocked tags.
* New: Renamed UAbilitySystemComponent::InternalServerTryActiveAbility to UAbilitySystemComponent::InternalServerTryActivateAbility.Code that was calling InternalServerTryActiveAbility should now call InternalServerTryActivateAbility.
* New: Continue to use the filter text for displaying gameplay tags when a tag is added or deleted. The previous behaviour cleared the filter.
* New: Don't reset the tag source when we add a new tag in the editor.
* New: Added the ability to query an ability system component for all active gameplay effects that have a specified set of tags. The new function is called GetActiveEffectsWithAllTags and can be accessed through code or blueprints.
* New: When root motion movement related ability tasks end they now return the movement component's movement mode to the movement mode it was in before the task started.
* New: Made SpawnedAttributes transient so it won't save data that can become stale and incorrect. Added null checks to prevent any currently saved stale data from propagating. This prevents problems related to bad data getting stored in SpawnedAttributes.
* API Change: AddDefaultSubobjectSet has been deprecated. AddAttributeSetSubobject should be used instead.
* New: Gameplay Abilities can now specify the Anim Instance on which to play a montage.

**[⬆ 返回目录](#table-of-contents)**

<a name="changelog-4.24"></a>
## 4.24

* Fixed blueprint node Attribute variables resetting to None on compile.
* Need to call [UAbilitySystemGlobals::InitGlobalData()](#concepts-asg-initglobaldata) to use [TargetData](#concepts-targeting-data) otherwise you will get ScriptStructCache errors and clients will be disconnected from the server. My advice is to always call this in every project now whereas before 4.24 it was optional.
* Fixed crash when copying a GameplayTag setter to a blueprint that didn't have the variable previously defined.
* UGameplayAbility::MontageStop() function now properly uses the OverrideBlendOutTime parameter.
* Fixed GameplayTag query variables on components not being modified when edited.
* Added the ability for GameplayEffectExecutionCalculations to support scoped modifiers against "temporary variables" that aren't required to be backed by an attribute capture.
	+ Implementation basically enables GameplayTag-identified aggregators to be created as a means for an execution to expose a temporary value to be manipulated with scoped modifiers; you can now build formulas that want manipulatable values that don't need to be captured from a source or target.
	+ To use, an execution has to add a tag to the new member variable ValidTransientAggregatorIdentifiers; those tags will show up in the calculation modifier array of scoped mods at the bottom, marked as temporary variables—with updated details customizations accordingly to support feature
* Added restricted tag quality-of-life improvements. Removed the default option for restricted GameplayTag source. We no longer reset the source when adding restricted tags to make it easier to add several in a row.
* APawn::PossessedBy() now sets the owner of the Pawn to the new Controller. Useful because Mixed Replication Mode expects the owner of the Pawn to be the Controller if the ASC lives on the Pawn.
* Fixed bug with POD (Plain Old Data) in FAttributeSetInittterDiscreteLevels.

**[⬆ 返回目录](#table-of-contents)**
