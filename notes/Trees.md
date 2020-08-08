# Drawing Presentable Trees
08/2008  
https://llimllib.github.io/pymag-trees/ (Originally published on Python Magazine)  

## Introdução

- *Layout* de árvores é um problema [NP-completo](https://en.wikipedia.org/wiki/NP-completeness)
- Muito dessa classe de problemas se deve a *D. E. Knuth, Optimum binary search trees, Acta Informatica 1 (1971)*

## Princípios

1. *The edges of the tree should not cross each other*
2. *All nodes at the same depth should be drawn on the same horizontal line. This helps make clear the structure of the tree*
3. *Trees should be drawn as narrowly as possible*
4. *A parent should be centered over its children*
	- Com isso passa a ser necessário considerar o contexto local dos nós
5. *A subtree should be drawn the same no matter where in the tree it lies*
	- Vindo de [Reingold & Tilford](https://www.computer.org/csdl/journal/ts/1981/02/01702828/13rRUxly8Uv)
	- Uma consequência é facilitar a transposição das sub-árvores, já que elas podem ser movidas como unidades, sem entrar nas folhas.
	- *Contours*: Listas de coordenadas máximas e mínimas de cada nível da árvore. Podem ser usados para resolver o problema da sobreposição.
		- Uma implementação básica necessita avaliar todos os nós em cada sub-árvore, aumentando a complexidade de execução.
			- *Threads* são uma maneira de otimizar o escaneamento de *contours* ao criar conexões hipotéticas entre folhas de diferentes níveis que não sejam conectadas.
		- Se uma árvore é mais profunda que outra, só é necessário avaliar a menor delas por inteiro. O excedente na outra árvore não pode, por definição, resultar em conflito de posição.
6. *The child nodes of a parent node should be evenly spaced*
	- Em árvores binárias, emerge naturalmente como consequência de ***4***.

## Código

Códigos de todos os exemplos dados no texto original estão disponíveis em https://github.com/llimllib/pymag-trees/  
Abaixo deixo a implementação em python do algorítmo para layout de árvores atraentes em O(n) elaborado por *Buchheim et. al.*  
	Não tem um motivo específico, só acho um código bonito escrito há mais de uma década.

```python
class DrawTree(object):
    def __init__(self, tree, parent=None, depth=0, number=1):
        self.x = -1.
        self.y = depth
        self.tree = tree
        self.children = [DrawTree(c, self, depth+1, i+1) 
                         for i, c
                         in enumerate(tree.children)]
        self.parent = parent
        self.thread = None
        self.offset = 0
        self.ancestor = self
        self.change = self.shift = 0
        self._lmost_sibling = None
        #this is the number of the node in its group of siblings 1..n
        self.number = number

    def left_brother(self):
        n = None
        if self.parent:
            for node in self.parent.children:
                if node == self: return n
                else:            n = node
        return n

    def get_lmost_sibling(self):
        if not self._lmost_sibling and self.parent and self != \
        self.parent.children[0]:
            self._lmost_sibling = self.parent.children[0]
        return self._lmost_sibling
    leftmost_sibling = property(get_lmost_sibling)

def buchheim(tree):
    dt = firstwalk(tree)
    second_walk(dt)
    return dt

def firstwalk(v, distance=1.):
    if len(v.children) == 0:
        if v.leftmost_sibling:
            v.x = v.left_brother().x + distance
        else:
            v.x = 0.
    else:
        default_ancestor = v.children[0]
        for w in v.children:
            firstwalk(w)
            default_ancestor = apportion(w, default_ancestor,
                                         distance)
        execute_shifts(v)

        midpoint = (v.children[0].x + v.children[-1].x) / 2

        ell = v.children[0]
        arr = v.children[-1]
        w = v.left_brother()
        if w:
            v.x = w.x + distance
            v.mod = v.x - midpoint
        else:
            v.x = midpoint
    return v

def apportion(v, default_ancestor, distance):
    w = v.left_brother()
    if w is not None:
        #in buchheim notation:
        #i == inner; o == outer; r == right; l == left;
        vir = vor = v
        vil = w
        vol = v.leftmost_sibling
        sir = sor = v.mod
        sil = vil.mod
        sol = vol.mod
        while vil.right() and vir.left():
            vil = vil.right()
            vir = vir.left()
            vol = vol.left()
            vor = vor.right()
            vor.ancestor = v
            shift = (vil.x + sil) - (vir.x + sir) + distance
            if shift > 0:
                a = ancestor(vil, v, default_ancestor)
                move_subtree(a, v, shift)
                sir = sir + shift
                sor = sor + shift
            sil += vil.mod
            sir += vir.mod
            sol += vol.mod
            sor += vor.mod
        if vil.right() and not vor.right():
            vor.thread = vil.right()
            vor.mod += sil - sor
        else:
            if vir.left() and not vol.left():
                vol.thread = vir.left()
                vol.mod += sir - sol
            default_ancestor = v
    return default_ancestor

def move_subtree(wl, wr, shift):
    subtrees = wr.number - wl.number
    wr.change -= shift / subtrees
    wr.shift += shift
    wl.change += shift / subtrees
    wr.x += shift
    wr.mod += shift

def execute_shifts(v):
    shift = change = 0
    for w in v.children[::-1]:
        w.x += shift
        w.mod += shift
        change += w.change
        shift += w.shift + change

def ancestor(vil, v, default_ancestor):
    if vil.ancestor in v.parent.children:
        return vil.ancestor
    else:
        return default_ancestor

def second_walk(v, m=0, depth=0):
    v.x += m
    v.y = depth

    for w in v.children:
        second_walk(w, m + v.mod, depth+1, min)
```
