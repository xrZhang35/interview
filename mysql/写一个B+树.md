```java
import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class BPlusTree {
    private static final int ORDER = 3; // 阶数
    private Node root;

    // 抽象节点类
    abstract class Node {
        List<Integer> keys = new ArrayList<>();
        abstract boolean isLeaf();
    }

    // 内部节点
    class InternalNode extends Node {
        List<Node> children = new ArrayList<>();

        @Override
        boolean isLeaf() {
            return false;
        }
    }

    // 叶子节点
    class LeafNode extends Node {
        List<Integer> values = new ArrayList<>();
        LeafNode next; // 用于范围查询

        @Override
        boolean isLeaf() {
            return true;
        }
    }

    public BPlusTree() {
        root = new LeafNode();
    }

    // 查找
    public Integer search(int key) {
        LeafNode leaf = findLeafNode(root, key);
        int index = Collections.binarySearch(leaf.keys, key);
        if (index >= 0) {
            return leaf.values.get(index);
        }
        return null;
    }

    // 插入
    public void insert(int key, int value) {
        Node newNode = insertRecursive(root, key, value);
        if (newNode != null) {
            InternalNode newRoot = new InternalNode();
            newRoot.keys.add(newNode.keys.get(0));
            newRoot.children.add(root);
            newRoot.children.add(newNode);
            root = newRoot;
        }
    }

    private Node insertRecursive(Node node, int key, int value) {
        if (node.isLeaf()) {
            LeafNode leaf = (LeafNode) node;
            int pos = Collections.binarySearch(leaf.keys, key);
            if (pos >= 0) {
                leaf.values.set(pos, value); // 更新
                return null;
            }
            int insertPos = -pos - 1;
            leaf.keys.add(insertPos, key);
            leaf.values.add(insertPos, value);

            if (leaf.keys.size() >= ORDER) {
                return splitLeaf(leaf);
            }
            return null;
        } else {
            InternalNode internal = (InternalNode) node;
            int i = 0;
            while (i < internal.keys.size() && key >= internal.keys.get(i)) {
                i++;
            }

            Node newChild = insertRecursive(internal.children.get(i), key, value);
            if (newChild != null) {
                int newKey = newChild.keys.get(0);
                internal.keys.add(i, newKey);
                internal.children.add(i + 1, newChild);

                if (internal.keys.size() >= ORDER) {
                    return splitInternal(internal);
                }
            }
            return null;
        }
    }

    private Node splitLeaf(LeafNode leaf) {
        LeafNode newLeaf = new LeafNode();
        int mid = (ORDER + 1) / 2;
        newLeaf.keys.addAll(leaf.keys.subList(mid, leaf.keys.size()));
        newLeaf.values.addAll(leaf.values.subList(mid, leaf.values.size()));
        leaf.keys.subList(mid, leaf.keys.size()).clear();
        leaf.values.subList(mid, leaf.values.size()).clear();

        newLeaf.next = leaf.next;
        leaf.next = newLeaf;

        return newLeaf;
    }

    private Node splitInternal(InternalNode internal) {
        InternalNode newInternal = new InternalNode();
        int mid = (ORDER + 1) / 2;

        newInternal.keys.addAll(internal.keys.subList(mid, internal.keys.size()));
        newInternal.children.addAll(internal.children.subList(mid, internal.children.size()));

        internal.keys.subList(mid - 1, internal.keys.size()).clear(); // 注意从 mid-1 开始
        internal.children.subList(mid, internal.children.size()).clear();

        return newInternal;
    }

    private LeafNode findLeafNode(Node node, int key) {
        if (node.isLeaf()) return (LeafNode) node;

        InternalNode internal = (InternalNode) node;
        int i = 0;
        while (i < internal.keys.size() && key >= internal.keys.get(i)) {
            i++;
        }
        return findLeafNode(internal.children.get(i), key);
    }

    // 打印 B+ 树结构
    public void printTree() {
        printNode(root, 0);
    }

    private void printNode(Node node, int level) {
        String indent = " ".repeat(level * 4);
        if (node.isLeaf()) {
            System.out.println(indent + "Leaf: " + node.keys);
        } else {
            InternalNode internal = (InternalNode) node;
            System.out.println(indent + "Internal: " + internal.keys);
            for (Node child : internal.children) {
                printNode(child, level + 1);
            }
        }
    }

    // 示例
    public static void main(String[] args) {
        BPlusTree tree = new BPlusTree();
        int[] keys = {10, 20, 5, 6, 12, 30, 7, 17};
        for (int key : keys) {
            tree.insert(key, key * 10);
        }

        tree.printTree();
        System.out.println("Search 12: " + tree.search(12));
        System.out.println("Search 25: " + tree.search(25));
    }
}

```

