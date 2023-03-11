---
title: Red-black tree implementation
author:
date: 2023-03-11 08:57:00 +0800
categories: [Technology]
tags: [data structure]
math: true
mermaid: true
---

```java
public class RedBlackTree<K extends Comparable<K>, V> {

    private static final boolean RED = true;
    private static final boolean BLACK = false;

    private Node<K, V> root;

    private static class Node<K, V> {
        K key;
        V value;
        Node<K, V> left, right;
        boolean color;
        int size;

        public Node(K key, V value, boolean color, int size) {
            this.key = key;
            this.value = value;
            this.color = color;
            this.size = size;
        }
    }

    private boolean isRed(Node<K, V> x) {
        if (x == null) return false;
        return x.color == RED;
    }

    private int size(Node<K, V> x) {
        if (x == null) return 0;
        return x.size;
    }

    public int size() {
        return size(root);
    }

    public boolean isEmpty() {
        return root == null;
    }
    // 这是一个红黑树中插入键值对的方法。它接收两个参数：键key和值value。
    public void put(K key, V value) {
	// 首先通过调用put方法，将键值对插入到红黑树中。
        root = put(root, key, value);
	// 将根节点的颜色设置为黑色。
        root.color = BLACK;
    }
    // 这是红黑树中的插入操作，它使用递归的方式向树中插入一个新节点，如果键已经存在，则更新该节点的值。该方法的返回值是插入后根节点的引用。
    private Node<K, V> put(Node<K, V> h, K key, V value) {
	// 如果当前节点为空，创建一个新节点并返回，颜色为红色。
        if (h == null) return new Node<>(key, value, RED, 1);
        int cmp = key.compareTo(h.key);
	// 如果当前节点不为空，比较插入键与当前节点键的大小，决定向左子树还是右子树递归插入。
        if (cmp < 0) h.left = put(h.left, key, value);
        else if (cmp > 0) h.right = put(h.right, key, value);
	// 如果插入的键与当前节点键相同，更新节点值。
        else h.value = value;

	// 在递归回溯的过程中，按照红黑树的性质，维护节点颜色和树的平衡。
        if (isRed(h.right) && !isRed(h.left)) h = rotateLeft(h);
        if (isRed(h.left) && isRed(h.left.left)) h = rotateRight(h);
        if (isRed(h.left) && isRed(h.right)) flipColors(h);

	// 最后更新节点的子树大小并返回节点引用。
        h.size = size(h.left) + size(h.right) + 1;
        return h;
    }
    // 这段代码实现了红黑树的左旋转操作。左旋转的目的是将红黑树中某个节点的右子节点变为该节点的父节点，同时该节点成为其右子节点的左子节点。
    private Node<K, V> rotateLeft(Node<K, V> h) {
	// 将该节点的右子节点保存到变量x中；
        Node<K, V> x = h.right;
	// 将该节点的右子节点指向变量x的左子节点；
        h.right = x.left;
	// 将变量x的左子节点指向该节点；
        x.left = h;
	// 将该节点的颜色设为红色；
        x.color = h.color;
	// 将变量x的颜色设为该节点的颜色；
        h.color = RED;
	// 更新该节点和变量x的子树大小。
        x.size = h.size;
	// 左旋转操作可以维护红黑树的平衡性和有序性，是红黑树中一个非常重要的操作。s
        h.size = size(h.left) + size(h.right) + 1;
        return x;
    }
    // 这段代码实现了红黑树的右旋转操作。
    private Node<K, V> rotateRight(Node<K, V> h) {
        Node<K, V> x = h.left;
        h.left = x.right;
        x.right = h;
        x.color = h.color;
        h.color = RED;
        x.size = h.size;
        h.size = size(h.left) + size(h.right) + 1;
        return x;
    }
    // flipColors 是红黑树中的一种操作，用于将一个节点和它的两个子节点从黑-红-红的颜色状态转换为红-黑-黑的颜色状态。
    private void flipColors(Node<K, V> h) {
        h.color = !h.color;
        h.left.color = !h.left.color;
        h.right.color = !h.right.color;
    }
    // 这段代码实现了红黑树的查找操作，其输入参数为一个键key，返回值为对应的值value。
    public V get(K key) {
	// 首先将搜索的起点设置为根节点root
        Node<K, V> x = root;
	// 在循环中根据比较key和当前节点x的键值的大小关系，选择向左子树或右子树进行搜索，直到找到key所对应的节点，返回该节点的值value。
        while (x != null) {
            int cmp = key.compareTo(x.key);
            if (cmp < 0) x = x.left;
            else if (cmp > 0) x = x.right;
            else return x.value;
        }
	// 如果搜索到最后x为null，则说明该红黑树中没有对应的key，返回null。
        return null;
    }
}
```