# How to read the syntax diagrams

The following structures are used in describing the syntax of GenevaERS program control statements and the GenevaERS logic text language.    

- Read the syntax diagrams from left to right, from top to bottom, following the path of the line.  
  - The ►► symbol indicates the beginning of a command.  
  - The ► symbol indicates that the command syntax is continued on the next line.
  - The ◄ symbol indicates that a command is continued from the preceding line.  
  - The ►◄ symbol indicates the end of a command.  
  
     
- Required items appear on the horizontal line (main path).
<pre>
    ►►─STATEMENT──required_item────────────────────────────────────►◄
</pre>
- Optional items appear below the main path.
<pre>
    ►►─STATEMENT──┬───────────────┬────────────────────────────────►◄
                  └─optional_item─┘   
</pre>
- If you can choose from two or more items, they appear in a vertical stack.
  
- If you must choose one of the items, one item of the stack appears on the main path.
<pre>
    ►►─STATEMENT──┬─required_choice1─┬─────────────────────────────►◄
                  └─required_choice2─┘   
</pre>
- If choosing one of the items is optional, the entire stack appears below the main path.
<pre>
    ►►─STATEMENT──┬──────────────────┬─────────────────────────────►◄
                  ├─optional_choice1─┤   
                  └─optional_choice2─┘   
</pre>
- If one of the optional items is the default, it appears above the main path and the remaining choices will be shown.
<pre>
                  ┌─default_choice1──┐   
    ►►─STATEMENT──┼──────────────────┼─────────────────────────────►◄
                  ├─optional_choice2─┤   
                  └─optional_choice3─┘   
</pre>
- Keywords appear in uppercase (for example, PARM1). They must be spelled exactly as shown.
  
- Variables appear in lowercase italics (for example, parmx). They represent user─supplied names or values.
<pre>
    ►►─STATEMENT──variable─────────────────────────────────────────►◄
</pre>
- An arrow returning to the left above the main line indicates an item that can be repeated.
<pre>
                  ┌─────────────────┐   
                  ▼                 |   
    ►►─STATEMENT────repeatable_item─┴──────────────────────────────►◄
</pre>
- A repeat arrow above a stack indicates that you can make more than one choice from the stacked items, or repeat a single choice.
  
- A repeat arrow above a stack of keywords means that you can enter one or more of the keywords. However, each keyword can be entered only once.
  
- A repeat arrow above a variable means that you can enter one or more values for the variable. However, each value can be entered only once.  
  
- If punctuation marks, parentheses, arithmetic operators, or other such symbols are shown, you must enter them as part of the syntax.

- Sometimes a single substitution represents a set of several parameters. For example, in the following diagram, the callout Parameter Block can be replaced by any of the interpretations of the subdiagram that is labeled **Parameter Block**:
  
<pre>
    ►►─STATEMENT──┬─CLAUSE1─────────────┬──────────────────────────►◄
                  └─┤ Parameter Block ├─┘   

    Parameter Block

    |──┬─PARM1───────────┬──────────────────────────────────────────|
       └─PARM2─┬─PARM3─┬─┘   
               └─PARM4─┘     
</pre>
