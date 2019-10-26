```
/**
 * 链表节点类
 */
class Node {
    constructor(ele) {
        this.ele = ele;
        this.next = null;
    }
}
/**
 * 链表类
 */
class NodeList {
    constructor(ele) {
        this.head = new Node(ele); //初始化链表的头节点
    }
    findPreNode(item) {
        let currentNode = this.head;
        while (currentNode && currentNode.next && currentNode.next.ele !== item) {
            if (currentNode.next) {
                currentNode = currentNode.next;
            } else {
                currentNode = null;
            }

        }
        return currentNode;
    }
    findNode(item) {
        let currentNode = this.head;

        while (currentNode && currentNode.ele !== item) {
            if (currentNode.next) {
                currentNode = currentNode.next;
            } else {
                currentNode = null;
            }
        }

        return currentNode;
    }
    findLastNode() {
        let currentNode = this.head;
        while (currentNode.next) {
            currentNode = currentNode.next;
        }
        return currentNode;
    }
    append(newItem, preItem) {
        let newNode = new Node(newItem);
        if (preItem) { //  判读是否是插入到指定节点后面，如果不是则插入到最后一个节点。
            let currentNode = this.findNode(preItem);
            newNode.next = currentNode.next;
            currentNode.next = newNode;
        } else {
            let lastNode = this.findLastNode();
            lastNode.next = newNode;
        }
    }
    remove(item) {
        let preNode = this.findPreNode(item); //  找到前一节点，将前一节点的next指向该节点的next
        if (preNode.next != null) {
            preNode.next = preNode.next.next;
        }
    }
    toString() {
        let currentNode = this.head;

        let strList = [];
        while (currentNode.next) {
            strList.push(JSON.stringify(currentNode.ele));
            currentNode = currentNode.next;
        }
        strList.push(JSON.stringify(currentNode.ele));

        return strList.join(' ==> ')
    }
}
let A = { name: 'A', age: 10 },
    B = { name: 'B', age: 20 },
    C = { name: 'C', age: 30 },
    D = { name: 'D', age: 40 },
    E = { name: 'E', age: 50 };

let nList = new NodeList(A);

nList.append(C);
nList.append(B);
nList.append(D);
nList.append(E, A);
console.log(" " + nList);
nList.remove(C);
console.log(" now " + nList)
```
