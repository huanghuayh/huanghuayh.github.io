---
layout: post
title: Trie Implementation
---

This is a very clever data structure, used to efficiently manage a word dictionary.

{% highlight java %}
class TrieNode {
    // Initialize your data structure here.
    public TrieNode() {
        this.children=new HashMap<Character,TrieNode >();
    }
    boolean is_word;
    String s;
    char cur_char;

    HashMap<Character,TrieNode > children;
}

public class Trie {
    private TrieNode root;

    public Trie() {
        root = new TrieNode();
    }

    // Inserts a word into the trie.
    public void insert(String word) {

        rec_insert(word, root,0);

    }

    private void rec_insert(String word, TrieNode node, int i){

        if(i>word.length()){
            return;
        }
        else if(i==word.length()){
            node.s=word;
            // node.cur_char=word.charAt(i-1);
            node.is_word=true;
            return;
        }
        else{
            if(node.children.containsKey(word.charAt(i))){
                rec_insert( word,  node.children.get(word.charAt(i)), i+1);
            }
            else{
                TrieNode temp_node=new TrieNode();
                temp_node.cur_char=word.charAt(i);
                node.children.put(word.charAt(i),temp_node);
                rec_insert( word,  node.children.get(word.charAt(i)), i+1);
            }
        }
    }

    // Returns if the word is in the trie.
    public boolean search(String word) {

        return rec_search(word,root,0);
    }

    private boolean rec_search(String word,TrieNode node, int i){
        if(i>word.length()){
            return false;
        }
        else if(i==word.length()){
            return node.is_word;
        }
        else{
            if(!node.children.containsKey(word.charAt(i))){
                return false;
            }
            else{
                return rec_search(word,node.children.get(word.charAt(i)),i+1);
            }
        }

    }

    // Returns if there is any word in the trie
    // that starts with the given prefix.
    public boolean startsWith(String prefix) {

        return rec_start(prefix,root,0);
    }

    private boolean rec_start(String word,TrieNode node, int i){
        if(i>=word.length()){
            return true;
        }
        else{
            if(!node.children.containsKey(word.charAt(i))){
                return false;
            }
            else{
                return rec_start(word,node.children.get(word.charAt(i)),i+1);
            }
        }

    }
}

{% endhighlight %}
