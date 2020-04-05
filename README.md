# 飲み込めていないメソッド

```
- forcelayoutの各設定項目
- linksメソッドとは？
- drag時のイベントとd.fxとは？
- alphaTargetとは？
- d3.event.activeとは？
```

# forcelayoutの各設定項目

```js
const simulation = d3.forceSimulation()
  .force("center",d3.forceCenter(width/2,height/2))
  .force("charge",d3.forceManyBody().strength(-50))
  .force("collide",d3.forceCollide(10).strength(0.9))
  .force("link",d3.forceLink().id(d => d.id))
```

- center

svg領域の中心を重力の中心とする設定

- charge

ノード同士が互いに反発する強さ。`d3.forceManyBody().strength(-50)`のように設定。

- collide

通常は下記のように各node毎の半径。

- link

何を線の両端の点とするか。この例では、`d3.forceLink().id(d => d.id))`として作曲家の名前同士を繋ぐ指定をしている。

```js
.force("collide",d3.forceCollide().radius(function(d){return d.r;})
```

node間が重なろうとした時の反発の設定。これがなければnodeは重なり放題。一律固定ならば、`d3.forceCollide(10)`のように設定。

# nodesメソッドやforceメソッド、linksメソッドとは？

ノードをフォースレイアウトの操作配下に置くためのもの

```js
// フォースレイアウト影響下に置くノードを指定
// これをしないと動かない
simulation
  .nodes(graph.nodes)
  .on("tick",ticked)

// フォースレイアウト影響下に置くノード同士をつなげる線を指定
// ノードの指定をした上で無いと動かない
simulation
  .force("link")
  .links(graph.links)
```

>紛らわしいのですが、画面上に描画されているnode(SVGのcircle要素）と内部でforcesimulationによって座標情報(d.xや>d.fy）などを計算されるnode(nodes配列の１要素）は繋がってはいますが別物であることに注意してください。
>
>node(SVGのcircle要素）は裏でnode(nodes配列の１要素）に操られていて、そのボスはforcesimulationだ

# drag時のイベントとd.fxとは？

d.fxとはノードの位置をフォースレイアウトに任せるのではなく固定値にする時に設定。`d.fx=null`とするとフォースレイアウト配下に位置の計算・設定が戻る。

```js
function dragstarted(d){
  if(!d3.event.active) simulation.alphaTarget(.7).restart();
  d.fx = d.x;
  d.fy = d.y
}

function dragged(d){
  d.fx = d3.event.x;
  d.fy = d3.event.y;
}

function dragended(d){
  if(!d3.event.active) simulation.alphaTarget(0);
  d.fx = null;
  d.fy = null;
}
```

# alphaTargetとは？

どのぐらいシャキシャキ動くか。0がデフォルト値である。0 ~ 1で設定可能。1に近いほどシャキシャキ動く。dragイベントで使う場合は、dragしている要素とリンクしている他のノードが引っ張られる際にどのぐらいシャキシャキと動くかの設定となる。

resart()が飲み込めていない(2020/4/6現在)のでdragのstart時に指定するおまじないと一旦捉える（いろんなサンプルでrestart()が呼び出されている事から）。

```js
simulation.alphaTarget(.7).restart();

simulation.alphaTarget(0);
```

[参考:D3.js v4/v5 force simulation 座標更新アルゴリズム/実行関数 まとめ](https://wizardace.com/d3-forcesimulation-info/)

# d3.event.activeとは？

consoleに出力すると以下の感じ。jsのイベントオブジェクトの親戚と理解して良さそう。

```
target: ƒ d(t)
type: "start"
subject: {id: "Champtercier", group: 1, index: 6, x: 73.96601334771319, y: 414.3430802687668, …}
identifier: "mouse"
active: 0
x: 77.88047497951936
y: 422.6133760343191
dx: 0
dy: 0
_: H {_: {…}}
sourceEvent: MouseEvent {isTrusted: true, screenX: 396, screenY: 637, clientX: 315, clientY: 490, …}
__proto__: Object
```

