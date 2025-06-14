# 【NO.1】【数据结构】史上最好理解的红黑树讲解，让你彻底搞懂红黑树

> 版权声明：本文为CSDN博主「小七mod」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。 原文链接：https://blog.csdn.net/cy973071263/article/details/1225438260

大家应该都学过平衡二叉树(AVLTree)，了解到AVL树的性质，其实平衡二叉树最大的作用就是查找,AVL树的查找、插入和删除在平均和最坏情况下都是O(logn)。AVL树的效率就是高在这个地方。如果在AVL树中插入或删除节点后，使得高度之差大于1。此时，AVL树的平衡状态就被破坏，它就不再是一棵二叉树；为了让它重新维持在一个平衡状态，就需要对其进行旋转处理, 那么创建一颗平衡二叉树的成本其实不小. 这个时候就有人开始思考，并且提出了红黑树的理论，红黑树在业界应用很广泛，比如 Java 中的 TreeMap，JDK 1.8 中的 HashMap、C++ STL 中的 map 均是基于红黑树结构实现的。那么红黑树到底比AVL树好在哪里？

## 1.红黑树简介

红黑树是一种自平衡的二叉查找树，是一种高效的查找树。它是由 Rudolf Bayer 于1978年发明，在当时被称为平衡二叉 B 树(symmetric binary B-trees)。后来，在1978年被 Leo J. Guibas 和 Robert Sedgewick 修改为如今的红黑树。红黑树具有良好的效率，它可在 O(logN) 时间内完成查找、增加、删除等操作。

## 2.为什么需要红黑树？

对于二叉搜索树，如果插入的数据是随机的，那么它就是接近平衡的二叉树，平衡的二叉树，它的操作效率（查询，插入，删除）效率较高，时间复杂度是O（logN）。但是可能会出现一种极端的情况，那就是插入的数据是有序的（递增或者递减），那么所有的节点都会在根节点的右侧或左侧，此时，二叉搜索树就变为了一个链表，它的操作效率就降低了，时间复杂度为O(N)，所以可以认为二叉搜索树的时间复杂度介于O（logN）和O(N)之间，视情况而定。那么为了应对这种极端情况，红黑树就出现了，它是具备了某些特性的二叉搜索树，能解决非平衡树问题，红黑树是一种接近平衡的二叉树（说它是接近平衡因为它并没有像AVL树的平衡因子的概念，它只是靠着满足红黑节点的5条性质来维持一种接近平衡的结构，进而提升整体的性能，并没有严格的卡定某个平衡因子来维持绝对平衡）。

## 3.红黑树的特性

在讲解红黑树性质之前，先简单了解一下几个概念：

- parent：父节点
- sibling：兄弟节点
- uncle：叔父节点（ parent 的兄弟节点）
- grand：祖父节点（ parent 的父节点）

首先，红黑树是一个二叉搜索树，它在每个节点增加了一个存储位记录节点的颜色，可以是RED,也可以是BLACK；通过任意一条从根到叶子简单路径上颜色的约束，红黑树保证最长路径不超过最短路径的二倍，因而近似平衡（最短路径就是全黑节点，最长路径就是一个红节点一个黑节点，当从根节点到叶子节点的路径上黑色节点相同时，最长路径刚好是最短路径的两倍）。它同时满足以下特性：

1. 节点是红色或黑色
2. 根是黑色
3. 叶子节点（外部节点，空节点）都是黑色，这里的叶子节点指的是最底层的空节点（外部节点），下图中的那些null节点才是叶子节点，null节点的父节点在红黑树里不将其看作叶子节点
4. 红色节点的子节点都是黑色
5. 红色节点的父节点都是黑色
6. 从根节点到叶子节点的所有路径上不能有 2 个连续的红色节点
7. 从任一节点到叶子节点的所有路径都包含相同数目的黑色节点

![image](https://i-blog.csdnimg.cn/blog_migrate/5f90bd60ae3a399dceb7f9d4031f1303.png)

根据上面的性质，我们来判断一下下面这课树是不是红黑树

![image-20221206204501442](https://i-blog.csdnimg.cn/blog_migrate/e4d1a2c570c111ab54bf9c1e50ae1dc7.png)

上面这棵树首先很容易就能知道是满足性质1-4条的，关键在于第5条性质，可能乍一看好像也是符合第5条的，但实际就会陷入一个误区，直接将图上的最后一层的节点看作叶子节点，这样看的话每一条从根节点到叶子结点的路径确实都经过了3个黑节点。

但实际上，在红黑树中真正被定义为叶子结点的，是那些空节点，如下图。

![image-20221206204513530](https://i-blog.csdnimg.cn/blog_migrate/3b9b9575ae889320f8433770d5816728.png)

这样一来，路径1有4个黑色节点（算上空节点），路径2只有3个黑色节点，这样性质5就不满足了，所以这棵树并不是一个红黑树节点。

注：下面的讲解图中将省略红黑树的null节点，请自行脑补

## 4.红黑树的效率

### 4.1 红黑树效率

红黑树的查找，插入和删除操作，时间复杂度都是O(logN)。

查找操作时，它和普通的相对平衡的二叉搜索树的效率相同，都是通过相同的方式来查找的，没有用到红黑树特有的特性。

但如果插入的时候是有序数据，那么红黑树的查询效率就比二叉搜索树要高了，因为此时二叉搜索树不是平衡树，它的时间复杂度O(N)。

插入和删除操作时，由于红黑树的每次操作平均要旋转一次和变换颜色，所以它比普通的二叉搜索树效率要低一点，不过时间复杂度仍然是O(logN)。总之，红黑树的优点就是对有序数据的查询操作不会慢到O(logN)的时间复杂度。

### 4.2 红黑树和AVL树的比较

1. AVL树的时间复杂度虽然优于红黑树，但是对于现在的计算机，cpu太快，可以忽略性能差异
2. 红黑树的插入删除比AVL树更便于控制操作
3. 红黑树整体性能略优于AVL树（红黑树旋转情况少于AVL树）

## 5.红黑树的等价变换

![image-20221206204623858](https://i-blog.csdnimg.cn/blog_migrate/a2cca1c6ade567025e1934ffb9dcad32.png)

上面这颗红黑树，我们来将所有的红色节点上移到和他们的父节点同一高度上，就会形成如下结构

![image-20221206204633358](https://i-blog.csdnimg.cn/blog_migrate/a46d00faaeb927f9df6aa18bd84371b9.png)

这个结构很明显，就是一棵四阶B树（一个节点最多放三个数据），如果画成如下的样子大家应该就能看的更清晰了。

![image-20221206204646607](https://i-blog.csdnimg.cn/blog_migrate/0619cd94d61c5028378bc1a98b95d9dc.png)

由上面的等价变换我们就可以得到如下结论：

1. 红黑树 和 4阶B树（2-3-4树）具有等价性
2. 黑色节点与它的红色子节点融合在一起，形成1个B树节点
3. 红黑树的黑色节点个数 与 4阶B树的节点总个数相等
4. 在所有的B树节点中，永远是黑色节点是父节点，红色节点是子节点。黑色节点在中间，红色节点在两边。

我们可以利用四阶B树与红黑树等价的性质，以红黑树转换成B树之后的节点情况来进行一个分类

![image-20221206204714100](https://i-blog.csdnimg.cn/blog_migrate/132df097609106ef0f29ccdda6b9bf88.png)

## 6.红黑树的操作

红黑树的基本操作和其他树形结构一样，一般都包括查找、插入、删除等操作。前面说到，红黑树是一种自平衡的二叉查找树，既然是二叉查找树的一种，那么查找过程和二叉查找树一样，比较简单，这里不再赘述。相对于查找操作，红黑树的插入和删除操作就要复杂的多。尤其是删除操作，要处理的情况比较多，下面就来分情况讲解。

### 6.1 旋转操作

在分析插入和删除操作前，先说明一下旋转操作，这个操作在后续操作中都会用得到。旋转操作分为左旋和右旋，左旋是将某个节点旋转为其右孩子的左孩子，而右旋是节点旋转为其左孩子的右孩子。这话听起来有点绕，所以还是请看下图：

![image-20221206204739533](https://i-blog.csdnimg.cn/blog_migrate/7e6480ddde36e71353c1ed132699155b.jpeg)

上图包含了左旋和右旋的示意图，这里以右旋为例进行说明，右旋节点 M 的步骤如下：

1. 将节点 M 的左孩子引用指向节点 E 的右孩子
2. 将节点 E 的右孩子引用指向节点 M，完成旋转

![image-20221206204755586](https://i-blog.csdnimg.cn/blog_migrate/4977abaa0077754b0e8687bec218c648.jpeg)

旋转操作本身并不复杂，上面分析了右旋操作，左旋操作与此类似，只是右旋转的逆操作。

### 6.2 插入操作

红黑树的插入过程和二叉查找树插入过程基本类似，不同的地方在于，红黑树插入新节点后，需要进行调整，以满足红黑树的性质。

性质1规定红黑树节点的颜色要么是红色要么是黑色，那么在插入新节点时，这个节点应该是红色还是黑色呢？答案是红色，原因也不难理解。如果插入的节点是黑色，那么这个节点所在路径比其他路径多出一个黑色节点，这个调整起来会比较麻烦（参考红黑树的删除操作，就知道为啥多一个或少一个黑色节点时，调整起来这么麻烦了）。如果插入的节点是红色，此时所有路径上的黑色节点数量不变，仅可能会出现两个连续的红色节点的情况。这种情况下，通过变色和旋转进行调整即可，比之前的简单多了。所以插入的时候将节点设置为红色，可以保证满足性质 1、2、3、5 ，只有性质4不一定满足，需要进行相关调整。如果是添加根节点，则将节点设定为黑色。

#### 6.2.1 插入操作的所有情况

我们在分析红黑树各种插入情况的时候，将其等价转换为B树，这样我们能够更直观的进行分类，首先确定几条性质：

1. B树中，新元素必定是添加到叶子节点中（最底层的节点）
2. 4阶B树所有节点的元素个数 x 都符合 1 ≤ x ≤ 3

![image-20221206204838937](https://i-blog.csdnimg.cn/blog_migrate/792f51f43483f01250e406919da15759.png)

在上一章节红黑树的等价变换中，我们讲到了红黑树转换成B树总共有四种情况，也就是上图中叶子节点这四种情况，那么在我们进行插入操作的时候，会将节点插入到所有的叶子节点中，总共就会有12种情况，其中四种情况满足红黑树的性质，8种情况不满足红黑树性质。

##### 6.2.1.1 满足红黑树性质4

有 4 种情况满足红黑树的性质 4 ：parent 为黑色节点。这四种情况不需要做任何额外的处理。

![image-20221206204904880](file://C:/Users/Administrator/AppData/Roaming/Typora/typora-user-images/image-20221206204904880.png?lastModify=1670851010)

##### 6.2.1.2 不满足红黑树性质4

有 8 种情况不满足红黑树的性质 4 ：parent 为红色节点（ Double Red ），其中左面4种属于B树节点上溢的情况（一个4阶B树节点中最多存放三个数，这四种情况本来已经有3个了，又插入了1个，变成了4个，超出了4阶B树节点的容量范围，这种情况称为上溢）。这八种情况需要进行额外的处理。

![image-20221206204923844](https://i-blog.csdnimg.cn/blog_migrate/2c44a49887629e36d37c93af635ae254.png)

#### 6.2.2 LL和RR插入情况

![image-20221206204942055](https://i-blog.csdnimg.cn/blog_migrate/b100aee0a05cfeff662bede6b6b7d594.png)

如上图，插入52和60的位置分别是RR情况和LL情况。

**RR情况**：父节点为祖父节点的右节点，插入节点为父节点的右节点

**LL情况**：父节点为祖父节点的左节点，插入节点为父节点的左节点

这两种情况很明显，插入节点为红色，父节点也为红色，父节点的子节点为红色显然违背了红黑树的性质四，我们需要对这种情况进行修复，使其重新满足红黑树性质。

**判定条件**：uncle 不是红色节点。

这里的两种情况，他们的插入节点都是没有叔父节点的，所以叔父节点也不可能是红色。

**案例修复：**

我们在红黑树等价转换那一章节也讲过了，红黑树等价转换成B树之后，B树节点的中间节点（父节点）都是黑色，两边的节点（子节点）都是红色。但是上面两种情况插入后，插入位置的B树节点并不满足这个条件，所以我们对其进行修复，使其满足B树节点的条件之后，也就重新恢复了红黑树性质。

B树节点中的中间节点大小介于两个子节点之间。以上图RR情况为例，插入节点52的原父节点应该放在B树节点中间的位置，应当将其染成黑色。插入节点52的原祖父节点46，应当将其转换为插入节点原父节点的子节点，所以将其染成红色。LL情况同理

完成染色之后，需要对原祖父节点进行单旋操作，来进行父节点，子节点的重新分配。以上图为例：

- RR情况应该原祖父节点46左旋，将插入节点的原父节点50旋转到中间的位置。
- LL情况应当原祖父节点76右旋，将插入节点的原父节点72旋转到中间的位置。

修复之后的结果如下图：

![image-20221206205052353](https://i-blog.csdnimg.cn/blog_migrate/8f1c3b9a4740b50155db03b2883f3072.png)

修复步骤总结：

1. parent 染成黑色，grand 染成红色
2. grand 进行单旋操作

- LL：右旋转
- RR：左旋转

#### 6.2.3 LR和RL插入情况

![image-20221206205131992](https://i-blog.csdnimg.cn/blog_migrate/65080205bcdf754e4c73fff07fe85951.png)

如上图，插入48和74的位置分别是RL情况和LR情况。

**RL情况**：父节点为祖父节点的右节点，插入节点为父节点的左节点

**LR情况**：父节点为祖父节点的左节点，插入节点为父节点的右节点

这两种情况和上面的两种情况一样，插入节点为红色，父节点也为红色，父节点的子节点为红色显然违背了红黑树的性质四，我们需要对这种情况进行修复，使其重新满足红黑树性质。

**判定条件**：uncle 不是红色节点。

这两种情况的插入节点也是没有叔父节点的。

**案例修复**：

B树节点中的中间节点大小介于两个子节点之间。以上图RL情况为例，插入节点48大小介于原父节点和原祖父节点之间，它应该是B树节点中的中间节点，所以将插入节点48染成黑色，将原祖父节点46染成红色来作为插入节点的子节点。LR情况同理

完成染色之后，需要进行双旋操作，来进行父节点，子节点的重新分配。以上图为例：

- RL情况应该原父节点50右旋，将插入节点48上移到原父节点50的高度，然后将插入节点的原祖父节点46进行左旋，将插入节点48移动到中间位置，成为中间节点。
- LR情况应该原父节点72左旋，将插入节点74上移到原父节点72的高度，然后将插入节点的原祖父节点76进行右旋，将插入节点74移动到中间位置，成为中间节点。

修复之后的结果如下图：

![image-20221206205210410](https://i-blog.csdnimg.cn/blog_migrate/bf8310abaf9e2f40bb1312248d509a75.png)

修复步骤总结：

1. 插入节点染成黑色，grand 染成红色
2. 进行双旋操作

- LR：parent 左旋转， grand 右旋转
- RL：parent 右旋转， grand 左旋转

#### 6.2.4 上溢的LL插入情况

![image-20221206205238114](https://i-blog.csdnimg.cn/blog_migrate/0f172b90bc02381233ff53f291375419.png)

如上图，插入10的位置是上溢的LL情况。

**上溢LL情况**：父节点为祖父节点的左节点，插入节点为父节点的左节点。并且构成的新的B树节点已经超过了B树节点容量大小范围。

这种情况和之前非上溢的四种情况一样，插入节点为红色，父节点也为红色，父节点的子节点为红色显然违背了红黑树的性质四，我们需要对这种情况进行修复，使其重新满足红黑树性质。

**判定条件**：uncle 是红色节点。满足这个条件的就都是上溢的情况，上溢的修复只需要染色，不需要旋转。

**案例修复**：

像这种上溢的情况，就需要从溢出的B树节点中选出一个节点进行向上合并，选择B树节点中中间的树去进行向上合并，这里中间的两个节点就是原父节点17和原祖父节点25，选这两个哪一个向上合并都是对的，但是我们最好选择以后方便操作的，很显然，应该选择原祖父节点25来进行向上合并，因为向上合并就是和最上层的38和55来组合成新的B树节点，向上合并的节点肯定是一个子节点，需要与上层相连，而原祖父节点25本身就已经和上层连接了，相对更加方便后续的操作。原祖父节点向上合并后，将其染成红色。

原祖父节点25向上合并后，它原来左右两边的节点需要分裂成两个子树，也就是原父节点17和插入节点10形成一个子树，原叔父节点33形成一个子树。这两个分裂形成的树都是以后25的子树。左边的子树由原父节点作为中间节点，染成黑色，右边的子树由原叔父节点作为中间节点，染成黑色。

修复之后的结果如下图：

![image-20221206205259582](https://i-blog.csdnimg.cn/blog_migrate/a04b6fa3361e82cf1a3c6f25ace749b7.png)

**修复步骤总结：**

1. parent、uncle 染成黑色
2. grand 向上合并

- 将向上合并的grand染成红色，相对上一层，就当做是新添加的节点，再次来一遍插入情况的判断，进行处理。

grand 向上合并时，可能继续发生上溢。这种情况就继续递归调用修复方法就可以了。若上溢持续到根节点，只需将根节点染成黑色即可（这个意思就是说断向上上溢，一直上溢到了B树的根节点位置了，只需要将向上合并的节点变成黑色作为红黑树的根节点即可。因为从B树根节点选择出来上溢的节点，肯定就是作为整个红黑树的根节点了）。

#### 6.2.5 上溢的RR插入情况

![image-20221206205334820](https://i-blog.csdnimg.cn/blog_migrate/cd61626130fbe806e4734357d1096b76.png)

如上图，插入36的位置是上溢的RR情况。

**上溢RR情况**：父节点为祖父节点的右节点，插入节点为父节点的右节点。并且构成的新的B树节点已经超过了B树节点容量大小范围。

**判定条件**：uncle 是红色节点

**案例修复**：

上溢RR情况的修复，和上溢LL情况基本一致，只是修复的位置不同，这里中间的两个节点就是原父节点33和原祖父节点25，选择原祖父节点25来进行向上合并，原祖父节点向上合并后，将其染成红色。

原祖父节点25向上合并后，它原来左右两边的节点需要分裂成两个子树，也就是原父节点33和插入节点36形成一个子树，原叔父节点17形成一个子树。这两个分裂形成的树都是以后25的子树。左边的子树由原叔父节点作为中间节点，染成黑色，右边的子树由原父节点作为中间节点，染成黑色。

修复之后的结果如下图：

![image-20221206205351829](https://i-blog.csdnimg.cn/blog_migrate/8743e0a6bec8cc6cbe2f851ee3efbba9.png)

**修复步骤总结**：

1. parent、uncle 染成黑色
2. grand 向上合并

- 染成红色（其实染成红色就已经是完成了向上合并，因为祖父节点和祖父节点的父节点的连接指向并没有变），当做是新添加的节点进行处理

#### 6.2.6 上溢的LR插入情况

![image-20221206205432793](https://i-blog.csdnimg.cn/blog_migrate/b83c164a3ab6bd0d13fd0f6d8fd47472.png)

如上图，插入20的位置是上溢的LR情况。

**上溢LR情况**：父节点为祖父节点的左节点，插入节点为父节点的右节点。并且构成的新的B树节点已经超过了B树节点容量大小范围。

**判定条件**：uncle 是红色节点

**案例修复**：

上溢LR情况的修复，和其他上溢情况基本一致，只是修复的位置不同，这里中间的两个节点就是原父节点17和原祖父节点25，选择原祖父节点25来进行向上合并，原祖父节点向上合并后，将其染成红色。

原祖父节点25向上合并后，它原来左右两边的节点需要分裂成两个子树，也就是原父节点17和插入节点20形成一个子树，原叔父节点33形成一个子树。这两个分裂形成的树都是以后25的子树。左边的子树由原父节点作为中间节点，染成黑色，右边的子树由原叔父节点作为中间节点，染成黑色。

修复之后的结果如下图：

![image-20221206205448935](https://i-blog.csdnimg.cn/blog_migrate/ed4189b7f108cc8001251229b70db4ad.png)

**修复步骤总结**：

1. parent、uncle 染成黑色
2. grand 向上合并

- 染成红色，当做是新添加的节点进行处理

#### 6.2.7 上溢的RL插入情况

![image-20221206205510158](https://i-blog.csdnimg.cn/blog_migrate/243a41d0399c31a601239f0f040154ad.png)

如上图，插入30的位置是上溢的RL情况。

**上溢RL情况**：父节点为祖父节点的右节点，插入节点为父节点的左节点。并且构成的新的B树节点已经超过了B树节点容量大小范围。

**判定条件**：uncle 是红色节点

**案例修复**：

上溢RL情况的修复，和其他上溢情况基本一致，只是修复的位置不同，这里中间的两个节点就是原父节点33和原祖父节点25，选择原祖父节点25来进行向上合并，原祖父节点向上合并后，将其染成红色。

原祖父节点25向上合并后，它原来左右两边的节点需要分裂成两个子树，也就是原父节点33和插入节点30形成一个子树，原叔父节点17形成一个子树。这两个分裂形成的树都是以后25的子树。左边的子树由原叔父节点作为中间节点，染成黑色，右边的子树由原父节点作为中间节点，染成黑色。

修复之后的结果如下图：

![image-20221206205526041](https://i-blog.csdnimg.cn/blog_migrate/88b38c6894fd23a4396c00949e3bbca0.png)

**修复步骤总结**：

1. parent、uncle 染成黑色
2. grand 向上合并

- 染成黑色，当做是新添加的节点进行处理

#### 6.2.8 插入情况总结

插入一共有12种情况：

1.插入节点的父节点是黑色的情况有4种 这种情况仍然会维持红黑树的性质，则不需要进行额外处理。 2.插入节点的父节点是红色的情况有8种 这种情况不满足红黑树的性质4，需要进行额外的修复处理。

这8种情况中： 1.叔父节点不是红色的情况有4种 这些情况都是非上溢，需要通过重新染色和旋转来进行修复 2.叔父节点是红色的情况有4种 这些情况都是上溢的，只需要通过祖父节点上溢合并和染色即可完成修复

### 6.3 删除操作

相较于插入操作，红黑树的删除操作则要更为复杂一些。B树中，最后真正被删除的元素都在叶子节点中。所以在红黑树中，被删除的节点一定也在最后一层。

![image-20221206205647333](https://i-blog.csdnimg.cn/blog_migrate/d6b4a51c7991d7661d503f9074eea93b.png)

#### 6.3.1 删除操作的所有情况

上面我们说删除节点一定都在最后一层，最后一层有红色节点和黑色节点，我们就以删除节点的颜色来区分删除操作的所有情况。

##### 6.3.1.1 删除红色节点

如果删除的节点是红色直接删除，不用作任何调整。因为删除最后一层的红色节点，并没有影响红黑树的任何性质。

![image-20221206205703888](https://i-blog.csdnimg.cn/blog_migrate/024faa1f888720a1599ba6ac3f46b15c.png)

##### 6.3.1.2 删除黑色节点

有3种情况：

1.拥有 2 个红色子节点的黑色节点

- 不可能被直接删除，因为会找它的子节点替代删除，因此不用考虑这种情况

2.拥有 1 个红色子节点的黑色节点 3.黑色叶子节点

![image-20221206205805669](https://i-blog.csdnimg.cn/blog_migrate/a1960369acce56beed5293bb79ef0b41.png)

#### 6.3.2 删除拥有1个红色子节点的黑色节点

![image-20221206205815162](https://i-blog.csdnimg.cn/blog_migrate/9bec01b929f84c425e80ae94ec24c7d5.png)

删除拥有1个红色子节点的黑色节点的情况，是需要我们做相关的处理的。这里删除的就是节点46和76，他们只有一个红色子节点。

对于一个二叉树来说，删除一个度为1的节点（度指的是一个节点的子节点个数），将其删除后需要用它唯一的子节点来进行替换。而红黑树的这种情况的判定条件，就是判定要替代删除节点的子节点是不是红色

**判定条件**：用以替代的子节点是红色节点

**案例修复**：

![image-20221206205831553](https://i-blog.csdnimg.cn/blog_migrate/8d322680b62c22a42186ca247aa98e23.png)

删除黑色节点46和76

第一步：

![image-20221206205838823](https://i-blog.csdnimg.cn/blog_migrate/4c03359160ca66747f0ac63166e03207.png)

将46与父节点的连接断开

第二步：

![image-20221206205846108](https://i-blog.csdnimg.cn/blog_migrate/a4a00aaac7388fdca8ad7371f9a6347e.png)

46唯一的红色子节点50作为代替46的节点，将其与46的父节点进行连接

第三步：

![image-20221206205852846](https://i-blog.csdnimg.cn/blog_migrate/048c0fb6fb216a24a1e6ec43333ba9f2.png)

断开46与50的连接，将46删除

删除节点76的过程与删除节点46相同

第一步：

![image-20221206205900124](https://i-blog.csdnimg.cn/blog_migrate/7019d45f42a1060eab3609954cf447a9.png)

第二步：

![image-20221206205907662](https://i-blog.csdnimg.cn/blog_migrate/abdfb23899d3787896b9eaa8d4adfc21.png)

第三步：

![image-20221206210339160](https://i-blog.csdnimg.cn/blog_migrate/28e1223144e9a2cfe06cb6fc9f6e77eb.png)

但是现在我们发现，80是红色节点，它的子节点72还是红色节点，这样明显不符合红黑树的性质，还需要进一步修复。

![](https://i-blog.csdnimg.cn/blog_migrate/de005765158d132219978666589f1430.png)

将替代的子节点染成黑色即可保持红黑树性质，修复完成

**修复步骤总结**：

1. 用删除节点的唯一子节点对其进行替代
2. 将替代节点染成黑色

#### 6.3.3 删除黑色叶子节点——删除节点为根节点

一棵红黑树只有一个黑色根节点（也就是唯一的一个叶子节点，整个红黑树只有这一个黑色节点），可直接删除该节点，无需做其他操作。

#### 6.3.4 删除黑色叶子节点——删除节点的兄弟节点为黑色

讲这种删除情况前先举一个例子

![image-20221206210409634](https://i-blog.csdnimg.cn/blog_migrate/659e35b0128bc353a47bc2ec6dab3656.png)

上面这个我们要删除节点88，该节点为黑色叶子节点，它的兄弟节点是黑色76。从B树的角度来看，如果删除88，因为四阶B树的节点中最少存有1个元素，如果不足，则会造成下溢。也就是需要从88的兄弟节点中借一个子节点出来。这就是这一节我们讨论的删除情况的核心修复思想。

##### 6.3.4.1 兄弟节点至少有1个红色子节点

下面三个图分别对应着兄弟节点至少有一个红色子节点的三种情况。删除节点为88，为黑色叶子节点，它的兄弟节点是76，为黑色。兄弟节点76都至少有一个红色子节点，三种情况分别为76拥有一个红色右子节点，76拥有一个红色左子节点，76拥有两个红色子节点。因为兄弟节点有红色子节点，所以可以借出一个节点来进行修复。

![image-20221206210837493](https://i-blog.csdnimg.cn/blog_migrate/9b231a9d2d15c672340ef258c233505d.png)

这三种情况，黑色叶子节点被删除后，会导致B树节点下溢（比如删除88），就可以从兄弟节点中借出一个红色子节点来进行修复。

**判定条件**：兄弟节点至少有 1 个红色子节点

**案例修复**：

1、兄弟节点有一个右子节点：

![image-20221206210854123](https://i-blog.csdnimg.cn/blog_migrate/cc63c7ce5ebd46811e496706c1df52d4.png)

先将88节点删除

![image-20221206210906579](https://i-blog.csdnimg.cn/blog_migrate/45507d40b3fb34962cb50c851592d6b5.png)

删掉之后，从B树的角度来看就出现了下溢，这个时候就需要父节点下来，在兄弟节点的子结点中找一个，将他升上去代替。具体的实现就是要对节点进行旋转。

![image-20221206210914933](https://i-blog.csdnimg.cn/blog_migrate/d08ed9a1faf9e0c36ce1eba7e8ac2ed8.png)

我们可以看出，80、76、78组成的树是一个LR的情况，先对76进行左旋转（可以将76看作父节点），这样78就上去了，再对80进行右旋转（可以将80看成祖父节点），80就下去了。

![image-20221206210927368](https://i-blog.csdnimg.cn/blog_migrate/66c2c4d39eb2ebda26b968aa43eb3c11.png)

旋转完了之后，如上图。将旋转完之后的中心节点（就是78、76、80组成的树的最中心的节点，这里就是78）进行重新染色，继承删除节点的父节点80的颜色。最后再将78、76、80组成的树的左右两个节点染成黑色即可完成修复。

2、兄弟节点有一个左子节点：

![image-20221206210940906](https://i-blog.csdnimg.cn/blog_migrate/d6bfa44656a1dc3a074723fda6dba4d2.png)

先将88节点删除

![image-20221206211007665](https://i-blog.csdnimg.cn/blog_migrate/2684bdf9785ff35c95326afd2f68c24e.png)

删掉之后，从B树的角度来看就出现了下溢，这个时候就需要父节点下来，在兄弟节点的子结点中找一个，将他升上去代替。具体的实现就是要对节点进行旋转。

![image-20221206211016938](https://i-blog.csdnimg.cn/blog_migrate/d09f751804b511c7ba8dc17b14cd2308.png)

我们可以看出，80、76、72组成的树是一个LL的情况，直接对80进行右旋（将80看成是祖父节点）。

![image-20221206211024793](https://i-blog.csdnimg.cn/blog_migrate/6608d04d451ea0a7dd5b9fbd265d2498.png)

旋转完了之后，如上图。将旋转完之后的中心节点（就是76、72、80组成的树的最中心的节点，这里就是76）进行重新染色，继承删除节点的父节点80的颜色。最后再将76、72、80组成的树的左右两个节点染成黑色即可完成修复。

3、兄弟节点有两个左右子节点：

![image-20221206211053071](https://i-blog.csdnimg.cn/blog_migrate/5f465c9f07b4d4c11a0b3c332fd35897.png)

先将88节点删除

![image-20221206211059812](https://i-blog.csdnimg.cn/blog_migrate/bd7ec69a3390c03a4c2c9fca33aa838b.png)

删除之后，其实可以有两种旋转可以进行修复，既可以使用LL方式进行旋转，也可以使用LR方式进行旋转。但是因为LL方式只需要旋转一次，我们就选用LL方式。

![image-20221206211115877](https://i-blog.csdnimg.cn/blog_migrate/5b752e82221414e9e8925a0d4a1f2786.png)

直接对80进行右旋

![image-20221206211108589](https://i-blog.csdnimg.cn/blog_migrate/e246fc0b2476e040449eeffc854cb2c5.png)

旋转完了之后，如上图。将旋转完之后的中心节点（就是78、72、76、80组成的树的最中心的节点，这里就是76）进行重新染色，继承删除节点的父节点80的颜色。最后再将78、72、76、80组成的树的左右两个节点染成黑色即可完成修复。

**修复步骤总结**：

1. 进行旋转操作
2. 旋转之后的中心节点继承父节点（删除节点的父节点）的颜色
3. 旋转之后的左右节点染为黑色

##### 6.3.4.2 兄弟节点没有红色子节点

当删除节点的兄弟节点没有红色节点可以借出的情况下，就需要父节点来向下合并进行修复，父节点向下和兄弟节点合并成新的B树节点来解决下溢。

判定条件：兄弟节点没有1个红色子节点

**案例修复**：

1、父节点为红色：

![image-20221206211156449](https://i-blog.csdnimg.cn/blog_migrate/020607c8a1fbf41af762bfb347791a7e.png)

删除节点88，出现下溢

![image-20221206211207479](https://i-blog.csdnimg.cn/blog_migrate/383b29065c2b087e1314c713e3f50f32.png)

因为兄弟节点76没有可以借出的红色节点，所以需要父节点80来向下与76合并进行修复

![image](https://i-blog.csdnimg.cn/blog_migrate/d112da967f5b0e0e01605da5873587b8.png)

将兄弟节点76染成红色，父节点80染成黑色即可完成修复

2、父节点为黑色：

![image-20221206211215639](https://i-blog.csdnimg.cn/blog_migrate/fd6a4e62361342c34508ae711fb8b660.png)

删除节点88，删除之后节点88就会出现下溢

![image-20221206211224207](https://i-blog.csdnimg.cn/blog_migrate/323e59a89e694fa7bcc58cd42bc3ed1f.png)

删除之后父节点80应该向下合并进行修复，但是因为父节点80为黑色，如果向下合并之后，其实就相当于80这个节点也出现了下溢。

![image-20221206211235311](https://i-blog.csdnimg.cn/blog_migrate/9acd6fa67e91c23692e05fbcf812adfa.png)

这个时候只需要把父节点当作被删除的节点进行处理即可

**修复步骤总结**：

1. 父节点向下与兄弟节点进行合并
2. 将兄弟染成红色、父节点染成黑色即可修复红黑树性质

- 如果父节点是黑色，直接将父节点当成被删除的节点处理，来修复父节点的下溢情况

#### 6.3.5 删除黑色叶子节点——删除节点的兄弟节点为红色

如果删除节点的兄弟节点为红色，这样删除节点出现下溢后没办法通过兄弟节点来进行修复。这就需要先把红黑树转换为兄弟节点为黑色的情况，就可以套用上面讲的修复方法来进行修复了。

![image-20221206211316257](https://i-blog.csdnimg.cn/blog_migrate/b9277c91bc8139703ee5c8304ae23305.png)

**判定条件**：兄弟节点是红色

**案例修复**：

![image-20221206211327772](https://i-blog.csdnimg.cn/blog_migrate/f8fdb2cd7173b7bc4512862879b56814.png)

删除88节点之前，需要先转换成兄弟节点为黑色的情况，当前88的兄弟节点是红色55。可以将其看作LL情况，对父节点88进行右旋转，这样55就被移动上去了，成了80的父节点。76也被移动上去了，成了80的子节点。

![image-20221206211355127](https://i-blog.csdnimg.cn/blog_migrate/6f181669a553786cb0797cef7ad4f3a7.png)

这种情况，删除节点88的兄弟节点就变成了黑色，并且没有红色子节点，可以继续套用之前讲的方法来进行修复了。

![image-20221206211403820](https://i-blog.csdnimg.cn/blog_migrate/747ec2e6c7958cd2c2741eb8843f33ed.png)

删除掉88，将80染成黑色，76染成红色，完成修复。

**修复步骤总结**：

1. 兄弟节点染成 BLACK，父节点染成染成 RED，对父节点进行右旋
2. 于是又回到兄弟节点是黑色的情况（侄子节点变为兄弟节点），继续使用兄弟节点为黑色的方法进行修复

## 7.红黑树的平衡

AVL是靠平衡因子来保持平衡的，比如平衡因子为1，那么左右子树的高度差就不能超过1，是一种强平衡。

对于红黑树而言，为何那5条性质，就能保证红黑树是平衡的？

- 因为那5条性质，可以保证红黑树等价于4阶B树

![image-20221206211437217](https://i-blog.csdnimg.cn/blog_migrate/390096dbbcdb0d1da5a5d3d687df6f5b.png)

B树比较矮，它本身就是平衡的，高度越小越平衡。

红黑树就是能保证这个树高度不会特别高，红黑树的最大高度是 2 ∗ log2(n + 1) ，依然是 O(logn) 级别，因为高度不会很大进而维持一种相对平衡的状态。相比AVL树，红黑树的平衡标准比较宽松：没有一条路径会大于其他路径的2倍。这是是一种弱平衡、黑高度平衡（黑高度只算黑色节点个数，红黑树的任何一条路径的黑色节点数一样，则黑高度都是一样）。

## 8.红黑树的平均时间复杂度

- 搜索：O(logn)
- 添加：O(logn)，O(1) 次的旋转操作
- 删除：O(logn)，O(1) 次的旋转操作

## 9.AVL树 vs 红黑树

### 9.1 AVL树

- 平衡标准比较严格：每个左右子树的高度差不超过1
- 最大高度是 1.44 ∗ log2 n + 2 − 1.328（100W个节点，AVL树最大树高28）
- 搜索、添加、删除都是 O(logn) 复杂度，其中添加仅需 O(1) 次旋转调整、删除最多需要 O(logn) 次旋转调整

### 9.2 红黑树

- 平衡标准比较宽松：没有一条路径会大于其他路径的2倍
- 最大高度是 2 ∗ log2(n + 1)（ 100W个节点，红黑树最大树高40）
- 搜索、添加、删除都是 O(logn) 复杂度，其中添加、删除都仅需 O(1) 次旋转调整

### 9.3 如何选择

- 搜索的次数远远大于插入和删除，选择AVL树；搜索、插入、删除次数几乎差不多，选择红黑树
- 相对于AVL树来说，红黑树牺牲了部分平衡性以换取插入/删除操作时少量的旋转操作，整体来说性能要优于AVL树
- 红黑树的平均统计性能优于AVL树，实际应用中更多选择使用红黑树

### 9.4 案例对比

10, 35, 47, 11, 5, 57, 39, 14, 27, 26, 84, 75, 63, 41, 37, 24, 96组成一棵树

#### 9.4.1 二叉搜索树

![image-20221206211548412](https://i-blog.csdnimg.cn/blog_migrate/a8c4df5e44a9780893be49701c1a1952.png)

非常不平衡

#### 9.4.2 AVL树

![image-20221206211557995](https://i-blog.csdnimg.cn/blog_migrate/8e4ba5011fcfb1fd5cd03563c2300af5.png)

最平衡

#### 9.4.3 红黑树

![image-20221206211607495](https://i-blog.csdnimg.cn/blog_migrate/d996d5b609dd1f8eec8408050d6e0b60.png)

相对比较平衡
