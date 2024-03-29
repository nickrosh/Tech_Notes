

Tries are data structures that derive from [Trees](Trees.md). They are designed for efficient search of strings. In a Trie, every Node is a letter and they connect to each other to form words. The final node in a _word_ has a special flag which denotes that from the root to that particular node creates a full word. Usually, string search in a Balanced Binary Tree would be $O(M log N)$, where M is maximum string length and N is number of keys in tree. With a Trie, Insert and search costs $O(M)$ where M is the key length, however the space requirements of a Trie is $O(26 M N)$, the 26 coming from the size of the alphabet.

![](../../Attachments/Pasted%20image%2020220413232023.png)


### Implementation

```python
class TrieNode:
    # Initialize your data structure here.
    def __init__(self):
        self.word = False
        self.children = {}
    
class Trie:
    def __init__(self):
        self.root = TrieNode()

    # Inserts a word into the trie.
    def insert(self, word: str) -> None:
        node=self.root
        for i in word:
            if i not in node.children:
                node.children[i] = TrieNode()
            node=node.children[i]
        node.word=True

    # Returns if the word is in the trie.
    def search(self, word: str) -> bool:
        node = self.root
        for i in word:
            if i not in node.children:
                return False
            node = node.children[i]
        return node.word

    # Returns if there is any word in the trie
    # that starts with the given prefix.
    def starts_with(self, prefix: str) -> bool:
        node = self.root
        for i in prefix:
            if i not in node.children:
                return False
            node = node.children[i]
        return True
```


## Wildcard Search

You can even make a Trie perform a wildcard search, so when inputted `ap*`, it will check to see if there is any 3 letter word starting with `ap`. This can be implemented with changing the `search` method to include a recursive [Depth First Search](../Algorithms/Depth%20First%20Search.md) of every child in the current node. If any of the children yield a match, the search returns true:

```python
def search(self, word: str) -> bool:
	def dfs(j, root):
		current = root
		for i in range(j, len(word)):
			c = word[i]
			if c != '.':
				if c not in current.children:
					return False
				current = current.children[c]
			elif c == '.':
				for child in current.children.values():
					if dfs(i+1, child):
						return True
				return False
		return current.word
	return dfs(0, self.root)
```