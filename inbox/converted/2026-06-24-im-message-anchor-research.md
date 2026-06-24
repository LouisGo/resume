---
type: imported_source
source_type: pdf
created_at: 2026-06-24
source_path: /Users/lou/Downloads/IM 消息锚点方案预研报告 (1).pdf
conversion_tool: pdfplumber
ocr_confidence: medium
---

# IM 消息锚点方案预研报告 PDF 转换文本

> 转换文本仅作为来源材料，不等于事实确认。

## Page 1

IM消息锚点⽅案预研报告
1. 先说结论
当前SDK只提供 和消息总数，本质是“按当前数组视图取⼀段消
feed_id + [from, end]
息”。它可以作为过渡分⻚能⼒，但不适合作为IM消息读取的⻓期核⼼抽象。
IM的正确抽象应是：
代码块
1 messageId / position = 消息⾝份锚点
2 before / after / around = 围绕锚点取上下⽂
3 cursor / total = 读取辅助信息，不是消息⾝份
描述“现在排第⼏”， 描述“是哪条消息”。IM会删除、撤回、补历史、跨端同
index anchor
步，位置会漂移，消息⾝份不应该漂移。
更准确地说： 可以继续作为统计、⽔位、兼容和估算信息存在，但不能承担“这条消
count/index
息是谁”的⾝份判断。
类似图书馆找⼀次性找相邻40本书的例⼦：
• 当前的按位逐条拿的⽅案（V1）：每次都按书架的指定位置往返40次，直到拿完40本书。
你要拿第120本到第160本，你需要跟管理员重复说40次：
>管理员，帮我拿第120本。
>管理员，帮我拿第121本。
>……
>⼀直来回40次。
但如果这时有⼈从中间抽⾛了⼀本书，⽐如第121本被删了，后⾯的书架位置就可能整体往前挪。
为了恢复正确状态，前端只能重新按位去重新拿40次：>管理员，重新帮我拿第120本到第160
本。插⼊新书、整理旧书同理。
此外当前⽅案的问题不只是慢，它最不能忍的地⽅在于假设在拿第2本书的时候管理员就迟迟找不
到，那么后⾯的38本书此时只能排队等着拿，你也只能在原地等待，啥也做不了。
• 正在开发调试中的异步分⻚⽅案（V2）：每次按书架位置批量拿40本书。
你跟图书管理员说：帮我拿第3排书架上，从左往右第120本到第160本书。
此时你可以去做其他的事，不需要站在原地等管理员拿40本书，可以在图书馆⾃由活动。

## Page 2

但这在书架不变的时候能⽤。但如果中间有⼈抽⾛⼀本书、插⼊⼀本新书、整理了⼀批旧书，原来
的“第130本”可能已经不是你要的那本了。你只能拿回来以后⾃⼰翻书名、核对作者，发现不对
再重找。
这就是现在的from/end+count：它能取⼀段消息，但它描述的是“当前排第⼏”，不是“我要哪
条消息”。
在IM场景⾥，删除、撤回、补历史、跨端同步、事件⻛暴都很常⻅。⼀步错，后⾯的恢复、跳转、
刷新、吸底都可能跟着错。
• ⽬标⽅案（V3）：按书的⾝份证先找到书
你跟管理员说：帮我找到唯⼀编号是xxx的这本书，再把它前后各20本相关书⼀起拿来。
同样⽆需等待管理员查找，可以⾃由在图书馆活动。
哪怕书架被整理过、前⾯插了新书、中间撤掉了旧书，管理员仍然知道你要的是哪⼀本。如果那本书
被下架了，管理员也能明确告诉你“这本没了，我给你它原来附近的书”。
这就是anchor⽅案：messageId/position是消息的⾝份证，before/after/around是围绕这条
消息取上下⽂。
V1解决能拿到的问题；V2能解决V1“拿得太慢太笨”的问题；V3解决的是V2“拿的是不是我要的
那条消息”的精准性问题。
2. 整体架构图

## Page 3

职责边界要调整为：
3. V3整体流程

## Page 4

⽆限滚动在这个模型⾥不需要全局 作为⾝份真相⸺窗⼝仍然可以向上、向下⽆限加载，下
count
⼀⻚的依据从"当前下标"变成"两端消息锚点"。SDK内部如需保留cursor、⻚token或局部index，
可作为实现细节，但不应要求前端⽤它还原消息⾝份。
4. 当前SDK⽅案V1的根因探讨
V1的出发点：是想让SDK帮客⼾端做虚拟列表。
虚拟列表会不断变化 和 ，于是SDK提供了类似
startIndex endIndex
、 拿总数+按位置拿单条数据的组合API，
get_feed_message_count get_feed_message
试图直接回答“这些下标对应什么消息”。
这个思路的问题是，把UI的视图下标，当成了消息系统的核⼼抽象。
本质上只是不同客⼾端当前窗⼝⾥的位置坐标，不是消息⾝份，PC和移动的实现各不相
uindex
同。
消息⾝份应该是 ，不是 。
messageId / position from / end
4.1V1架构图

## Page 5

这个架构的关键问题，不是“有没有缓存”，⽽是滚动路径被设计成依赖SDK同步供数。
4.2为什么这条路不合理？
虚拟列表的 是视图坐标，不是消息⾝份。
start/end
滚动过程中， 会⾼频变化。如果每次变化都要经过IPC，再等SDK返回，就等于把最敏
start/end
感的滚动路径，挂在了⼀条⻓链路上：
代码块
1 滚动 -> 计算 index range -> IPC -> SDK -> cache / sqlite / server -> 返回 -> 渲染
命中缓存时看起来正常，只是因为数据碰巧在本地。
⼀旦缓存未命中，问题就会暴露：客⼾端不是在等渲染，⽽是在等SDK补数据。
⼀个10000条消息的超⼤群会把问题放⼤：

## Page 6

问题不在[0,20]本⾝，⽽在于SDK把“取20条消息”，变成了“为了维护全局index连续性，补⻬
10000条历史”。
这就是抽象泄漏。
4.3消息不能有空洞问题的讨论
如果消息不能有空洞的理由，是因为想解决⽓泡连续样式：
• 同⼀个⼈连续发3条消息；
• 第⼀条显⽰头像；
• 中间和最后⼀条圆⻆不同；
• 需要知道相邻消息是否同⼀发送⼈、是否同⼀时间段。
但⽓泡连续性只需要局部上下⽂，不需要全局连续index。
例如渲染⼀段窗⼝，只要知道窗⼝边界前后各少量消息即可：
代码块
1 before: 1
2 current: 当前窗⼝
3 after: 1
这样就⾜够判断头像、圆⻆、分组，不需要SDK为了样式去维护整条历史的连续下标。
4.4为什么锚点⽅案V3更好？
两者差别⾮常⼤。

## Page 7

• V1维护的是“全局数组视图”。
• 锚点⽅案只维护“消息⾝份+局部上下⽂”。
后者更⼩，更稳定，也更符合IM的真实问题。
5. 本次尝试的优化⽅案V2：异步chunk按索引拿的问题
SDK当前核⼼能⼒：
代码块
1 // 拿消息总数
2 getFeedMessageCount(feedId);
3
4 // 拿批量消息
5 multi_get_feed_messages({
6 feed_id: string,
7 from: number,
8 end: number,
9 });
前端被迫维护：
代码块
1 total: number
2 windowRange: [fromUIndex, endUIndex]
3 items: Message[]
新的消息异步分⻚请求重构已经在renderer内部引⼊ 做恢复、跳转、事
position/messageId
件匹配和tail⽔位判断，但SDK仍然只⽀持 。导致现在是折中⽅案：前端知道锚点
uindex range
更可靠，但读取消息仍必须换算成index。

## Page 8

根本问题在于：SDK只理解 ，不理解“我要的是某条消息附近的上下⽂”。于是前端需
from/end
要额外维护 的局部索引，并在跳转、恢复、事件处理时反复
uindex -> position/messageId
做identity校验。这是⽬前批量索引⽅案开发过程中主要遭遇到的障碍和坑。
6. UIindex+count模型（V1和V2）的局限
核⼼差异：index是可变视图坐标，anchor是稳定消息⾝份。count/index不是没有⽤，⽽是不应该
被迫承担⾝份语义。
7. 主流IM的共同⽅向
公开API不能等同于闭源客⼾端内部实现，但它能说明成熟IM服务层通常会暴露消息⾝份、时间/序
列锚点或上下⽂分⻚能⼒，⽽不是只给全局数组下标。

## Page 9

结论：成熟系统可以有cursor、timestamp、position、messageid等不同实现，但共同点是：业务
操作围绕消息⾝份，不围绕UI下标。
8. 建议⽬标SDK契约
下⾯是沟通阶段建议收敛的⽬标能⼒，不要求⼀次性全部落地，但每个语义点都需要SDK和客⼾端共
同确认，否则前端仍会回到猜index的旧模型。
8.1核⼼类型
建议把anchor定义成消息⾝份，⽽不是⻚码：
代码块
1 type MessageAnchor = {
2 /**
3 * 消息唯⼀ ID。优先⽤于精确⾝份匹配。
4 */
5 messageId?: string;
6 /**
7 * 稳定、递增、可排序、允许空洞的服务端顺序。
8 * 如果底层是 64-bit 序列，需要明确是 JS safe integer 还是 string。
9 */
10 position?: number | string;
11 };
12
13 type MessagePage = {
14 items: Message[];
15 /**
16 * around 场景下表⽰⽬标 anchor 的命中状态。
17 */
18 anchorStatus?: 'hit' | 'deleted' | 'not_found' | 'mismatch';
19 hasMoreBefore: boolean;
20 hasMoreAfter: boolean;
21 beforeCursor?: string;
22 afterCursor?: string;
23 /**
24 * ⽤于防旧响应覆盖新窗⼝。可以是会话内版本、服务端 snapshot id 或 SDK epoch。
25 */
26 snapshotVersion?: string;
27 /**
28 * 统计和兼容信息，只能作为 hint，不能作为消息⾝份依据。
29 */
30 indexHint?: number;
31 totalHint?: number;
32 };

## Page 10

anchor语义建议固定为：
• 和 同时提供时，SDK必须校验它们是否指向同⼀条消息；不⼀致时返
messageId position
回 ，不要静默降级成任意⼀个字段。
mismatch
• 只提供 或只提供 时，SDK可以尽⼒定位，但返回值必须明确是否命中
messageId position
原消息。
• 是排序锚点，不是连续计数；允许空洞，不能⽤差值推导消息数。
position
• 如果anchor已删除，SDK应返回 或最近邻上下⽂，⽽不是让前端⽤旧 猜
deleted index
测。
• 建议 统⼀按旧消息到新消息排序，便于rendererprepend/append；如果SDK选择其他
items
顺序，也必须显式声明。
• 默认不包含anchor； 默认包含anchor。这个inclusive/exclusive规
before/after around
则必须固定，否则前端会出现重复或漏消息。
• 短⻚允许存在，但需要通过 或 明确边界，⽽不
hasMoreBefore/hasMoreAfter cursor
是靠 让前端猜。
items.length < limit
8.2⽬标读取接⼝
代码块
1 getLatestMessages(feedId: string, limit: number): Promise<MessagePage>;
2
3 getMessagesBefore(
4 feedId: string,
5 anchor: MessageAnchor,
6 limit: number,
7 ): Promise<MessagePage>;
8
9 getMessagesAfter(
10 feedId: string,
11 anchor: MessageAnchor,
12 limit: number,
13 ): Promise<MessagePage>;
14
15 getMessagesAround(
16 feedId: string,
17 anchor: MessageAnchor,
18 before: number,
19 after: number,
20 ): Promise<MessagePage>;
21
22 locateMessage(
23 feedId: string,

## Page 11

24 anchor: MessageAnchor,
25 ): Promise<{
26 exists: boolean;
27 message?: Message;
28 indexHint?: number;
29 totalHint?: number;
30 nearestBefore?: MessageAnchor;
31 nearestAfter?: MessageAnchor;
32 }>;
这组接⼝的重点不是“前端少调⼀次接⼝”，⽽是把消息顺序、锚点定位和上下⽂窗⼝交回SDK维
护。
renderer只消费结果并处理滚动补偿、⾼亮、吸底和窗⼝切换。
8.3兼容期接⼝关系
建议降级为兼容接⼝或直接被重构：
multi_get_feed_messages
• 新的anchor/contextAPI成为跳转、恢复、事件reconcile和⻓期分⻚的推荐路径。
• 继续作为统计、⽔位和兼容hint，不再作为消息⾝份来源。
count/index
9. ⽆限上下滑动窗⼝怎么做
窗⼝只保存当前连续消息段，两端就是下⼀次加载的锚点：
代码块
1 items: Message[]
2 oldestAnchor = items[0].messageId / position
3 newestAnchor = items[items.length - 1].messageId / position

## Page 12


简单⽰例，假设SDK返回items已按旧消息到新消息排序：
代码块
1 async function loadBefore(feedId: string) {
2 const oldest = items[0];
3 if (!oldest) return;
4
5 const page = await sdk.getMessagesBefore(
6 feedId,
7 {
8 messageId: oldest.id,
9 position: oldest.position,
10 },

## Page 13

11 40,
12 );
13
14 items = [...page.items, ...items];
15 }
16
17 async function loadAfter(feedId: string) {
18 const newest = items.at(-1);
19 if (!newest) return;
20
21 const page = await sdk.getMessagesAfter(
22 feedId,
23 {
24 messageId: newest.id,
25 position: newest.position,
26 },
27 40,
28 );
29
30 items = [...items, ...page.items];
31 }
⽆限滚不需要把全局count当成⾝份真相才成⽴。窗⼝加载只需要两端消息⾝份；
仍可⽤于滚动条估算、尾部⽔位、统计展⽰和兼容逻辑。
count/totalHint
10. 关键IM功能如何落地
10.1滚动恢复

## Page 14

如果锚点消息已删除，SDK应明确返回 或邻近消息，前端再恢复到最近合理位置。
exists=false
不要让前端依赖index去猜。
10.2quote/mention/search/pin跳转
代码块
1 getMessagesAround(feedId, targetMessageAnchor, 30, 30);
• 当前窗⼝命中：直接滚动并⾼亮。
• 当前窗⼝未命中：加载⽬标上下⽂窗⼝，再滚动并⾼亮。
10.3发送乐观更新

## Page 15

乐观消息不进⼊正式分⻚边界。正式消息必须由 或 精确对账。
clientTempId oldTempId
这⾥需要提前和SDK对⻬两个细节：
• 由谁⽣成、SDK是否原样透传、重试发送时是否具备幂等语义。
clientTempId
• 如果服务端消息push先于sendsuccess回来，SDK需要提供可去重的
clientTempId /
关系，避免前端先append⼀条正式消息，再把optimistic替换
oldTempId / messageId
成第⼆条正式消息。
10.4接收、编辑、删除
事件必须带消息⾝份和事件类型：
代码块
1 type MessageEvent =
2 | {
3 type: 'tail'; // 具体的尾部消息事件
4 feedId: string;
5 messageId: string;
6 position: number | string;
7 eventId?: string;
8 sequence?: number;
9 }
10 | {
11 type: 'exact'; // 具体的单条消息事件
12 feedId: string;
13 messageId: string;

## Page 16

14 position: number | string;
15 eventId?: string;
16 sequence?: number;
17 subtype?: 'edit' | 'reaction' | 'read' | 'image-download' | 'urgent';
18 }
19 | {
20 type: 'structural'; // 结构性的重建，需要重拉窗⼝
21 feedId: string;
22 messageId?: string;
23 position?: number | string;
24 eventId?: string;
25 sequence?: number;
26 subtype?: 'delete' | 'recall' | 'clear' | 'unknown-count-change';
27 };
处理规则：
是客⼾端消费层的分类，不⼀定要求SDK内部也这样命名。但SDK事
tail/exact/structural
件⾄少要提供⾜够信息，让客⼾端可以稳定映射：
• 是尾部新增，还是窗⼝内某条消息变化。
• 等subtype。
edit/read/reaction/image-download/delete/recall/clear
• 可去重、可排序或可判断新旧的事件标识，例如 或单调 。
eventId sequence
• 缺少 的broadupdate要明确标为broad，客⼾端只能做视⼝刷新或
messageId/position
structuraldirty，不能假装是exact。
11. 对SDK的分阶段诉求
不建议把这件事包装成“⼀次性重写SDK分⻚”。更现实的推进⽅式是分阶段建⽴anchor能⼒：

## Page 17

最⼩可接受⽅向：
1. 应成为过渡性的兼容接⼝，⽽不是IM消息读取的唯⼀核⼼能
multi_get_feed_messages
⼒。
2. SDK必须稳定返回 和可排序的 ，并明确position的数值范围。
messageId position
3. SDK⾄少提供anchor定位能⼒；完整⽬标是⽀持
before/after/around message
。
anchor
4. SDK事件必须能被客⼾端稳定映射为tail、exact或structural，并尽量携带
。
messageId/position
5. 发送链路必须⽀持 的精确对账。
clientTempId -> server messageId/position
6. 可以保留为统计、⽔位、兼容和诊断信息，但不能要求前端⽤它判断消息⾝份。
count/index
最终⽬标不是让前端少写代码，⽽是让消息语义放回正确的位置：SDK负责消息⾝份、顺序和上下⽂
读取，前端负责窗⼝渲染、滚动补偿和交互体验。
12. 参考资料
• TelegramTDLibgetChatHistory
• TelegramTDLibupdateMessageSendSucceeded
• Slackconversations.history
• Slackchat.postMessage
• DiscordGetChannelMessages
• Matrix/context/{eventId}
• Lark/⻜书消息API
会议纪要

## Page 18

 IM消息锚点⽅案预研报告会议纪要 
