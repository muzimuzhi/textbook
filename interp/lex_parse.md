# Lexing and Parsing

You *could* code your own lexer and parser from scratch.  But many
languages include tools for automatically generating lexers and parsers
from formal descriptions of the syntax of a language.  The ancestors of
many of those tools are [lex][lex] and [yacc][yacc], which generate
lexers and parsers, respectively; lex and yacc were developed in the
1970s for C.  

As part of the standard distribution, OCaml provides lexer and parser
generators named [ocamllex and ocamlyacc][ocamllexyacc]. There is a more
modern parser generator named [menhir][menhir] available through opam;
menhir is "90% compatible" with ocamlyacc and provides significantly
improved support for debugging generated parsers. 

[lex]: https://en.wikipedia.org/wiki/Lex_(software)
[yacc]: https://en.wikipedia.org/wiki/Yacc
[ocamllexyacc]: http://caml.inria.fr/pub/docs/manual-ocaml/lexyacc.html
[menhir]: http://gallium.inria.fr/~fpottier/menhir/

## Lexers

Lexer generators such as lex and ocamllex are built on the theory of
deterministic finite automata, which you learned in CS 2800.  Recall
that such automata accept *regular languages*, which can be described
with *regular expressions*. So, the input to a lexer generator is a
collection of regular expressions that describe the tokens of the
language.  The output is an automaton implemented in a high-level
language, such as C (for lex) or OCaml (for ocamllex).

That automaton itself takes files (or strings) as input, and each
character of the file becomes an input to the automaton.  Eventually the
automaton either *recognizes* the sequence of characters it has received
as a valid token in the language, in which case the automaton produces
an output of that token and resets itself to being recognizing the next
token, or *rejects* the sequence of characters as an invalid token.

## Parsers

Parser generators such as yacc and menhir are similarly built on the
theory of automata, but in this case, *pushdown automata*, which are not
always covered in CS 2800.  They are like finite automata except they
also maintain a stack onto which they can push and pop symbols.  The
stack enables them to accept a bigger class of languages, which are
known as *context-free languages* (CFLs).  One of the big improvements of
CFLs over regular languages is that CFLs can express the idea that delimiters must be
balanced&mdash;for example, that every opening parenthesis must be
balanced by a closing parenthesis.

Just as regular languages can be expressed with a special notation
(regular expressions), so can CFLs.  *Context-free grammars* are used
to describe CFLs.  A context-free grammar is a set of *production rules* of
that describe how one symbol can be replaced by other symbols.  For example,
the language of balanced parentheses, which includes strings such
as (()) and ()() and (()()), but not strings such as ) or ((), 
is generated by these rules:

* \\(S \\rightarrow (S)\\)
* \\(S \\rightarrow SS\\)
* \\(S \\rightarrow \\epsilon\\)

The symbols occurring in those rules are \\(S\\), (, and ).  The
\\(\epsilon\\) denotes the empty string.  Every symbol is either a
*nonterminal* or a *terminal*, depending on whether it is a token of the
language being described.  \\(S\\) is a nonterminal in the example
above, and ( and ) are terminals.

BNF thus is notation for context-free grammars, and the input to a
parser generator is typically a BNF description of the language's
syntax.  The output of the parser generator is a program that recognizes
the language of the grammar.  As input, that program expects the output
of the lexer.  As output, the program produces a value of the AST type
that represents the string that was accepted. The programs output by the
parser generator and lexer generator are thus dependent upon on another
and upon the AST type. 
