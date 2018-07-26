---
layout: post
title: "Clean Reddit texts with markdown and emoticons"
description: "daily post"
comments: true
keywords: "regrex,python"
---

If you want to clean the texts with markdown grammar, you can follow through the content.


#### 1. Related Regular Expression Patterns

Following lists the regular expression syntax that is available in Python.

| Pattern | Description                              |
| ------- | ---------------------------------------- |
| ^       | match beginning of the line.             |
| $       | match end of line.                       |
| .       | match any single character except '\n'.  |
| [...]   | match any single character in brackets.  |
| re*     | match 0 or more occurrences of preceding expression |
| re+     | match 1 or more occurrences of preceding expression |
| re?     | match 0 or 1 occurrence of preceding expression. |
| re{n}   | match exactly n number of occurrences of preceding expression. |
| \1...\9 | match n-th grouped sub-expression.       |
| (re)    | Groups regular expressions and remembers matched text. |


#### 2. Parse Markdown Grammar
Here are related regular expressions for parsing markdown, which are from [inlinepatterns.py](https://github.com/Python-Markdown/markdown/blob/master/markdown/inlinepatterns.py).
```python
# \<
ESCAPE_RE = r'\\(.)'
# *emphasis*
EMPHASIS_RE = r'(\*)([^\*]+)\2'
# **strong**
STRONG_RE = r'(\*{2}|_{2})(.+?)\2'
# ***strongem*** or ***em*strong**
EM_STRONG_RE = r'(\*|_)\2{2}(.+?)\2(.*?)\2{2}'
# ***strong**em*
STRONG_EM_RE = r'(\*|_)\2{2}(.+?)\2{2}(.*?)\2'
# _smart_emphasis_
SMART_EMPHASIS_RE = r'(?<!\w)(_)(?!_)(.+?)(?<!_)\2(?!\w)'
# _emphasis_
EMPHASIS_2_RE = r'(_)(.+?)\2'
# [text](url) or [text](<url>) or [text](url "title")
LINK_RE = NOIMG + BRK + \
    r'''\(\s*(<.*?>|((?:(?:\(.*?\))|[^\(\)]))*?)\s*((['"])(.*?)\12\s*)?\)'''
# ![alttxt](http://x.com/) or ![alttxt](<http://x.com/>)
IMAGE_LINK_RE = r'\!' + BRK + r'\s*\(\s*(<.*?>|([^"\)\s]+\s*"[^"]*"|[^\)\s]*))\s*\)'
# [Google][3]
REFERENCE_RE = NOIMG + BRK + r'\s?\[([^\]]*)\]'
# [Google]
SHORT_REF_RE = NOIMG + r'\[([^\]]+)\]'
```


#### 3. Remove Markdown Token in Reddit
Here is one example for cleaning texts on reddit with markdown grammer, which also contains emotions. The pair `xxx_re, xxx_sub` is used for identify the `xxx` pattern and then convert it into simple form.

```python
# The actual regular expressions for patterns
# *emphasis*
EMPHASIS_RE = r'([_\*])([^\*]+)\1'
EMPHASIS_SUB = r'\2'
# **strong**
STRONG_RE = r'(\*{2}|_{2}|\~{2 })(.+?)\1'
STRONG_SUB = r'\2'
# [text](url) or [text](<url>) or [text](url "title")
LINK_RE=r'(?<!\\)\[(?P<link>.+?)\]\((?P<url>.+?)\)'
LINK_SUB=r'\1'
# ![alltext](http://x.com) or ![alttext](<http://x.com/>)
IMAGE_LINK_RE=r'\!(?<!\\)\[(?P<link>.+?)\]\((?P<url>.+?)\)'
IMAGE_LINK_SUB=r'\1'
# <me@example.com>
AUTOMAIL_RE=r'<([^> \!]*@[^> ]*)>'
AUTOMAIL_SUB=' email '
URL_RE=r"((?:[Ff]|[Hh][Tt])[Tt][PpDIGITS=][Ss]?://[^>]*)"
URL_SUB=r' url '
DIGITS_RE=r"[+-]? *(?:\d+(?:\.\d*)?|\.\d+)(?:[eE][+-]?\d+)?"
DIGITD_SUB=' digits'
# html tokens
HTML_RE=r"(&[\#a-zA-Z0-9]*;)"
HTML_SUB=r""
# repeated word character, like: Nachoooooo  WOOOOOSHHHH
REPEATED_RE=r"([\w\.])\1{2,}"
REPEATED_SUB=r"\1\1"
# 'hahahahahah, hihihi, huehue, xixixi, abcdefgefgefg, hurrhurr, ballistics, ballballball, ballerina, ballerinanana'
REPEAT_WORD_RE=r"\b[\w]*?([\w\. ]{2,}?)\1+[\w]*?\b"
REPEAT_WORD_SUB=r"\1"
# duplicated punctuation
REPEAT_PUNC_RE= r'([\!\"\#\$\%\&\'\(\)\*\+\-\/\:\;\<\=\>\?\@\[\\\\\]\_\`\{\|\}\~ ])\1{2,}'
REPEAT_PUNC_SUB=r"\1"
SPECIAL_PUNC_RE=r"([\.\,])\1{2,}"
SPECIAL_PUNC_SUB=r" \1_ "
SPECIAL_SPACE_RE=r"[/~*\.\' ]"
SPECIAL_SPACE_SUB=r" "
```

We just transfer the emoticons into related words: happy, sad and etc.

```python
# tweet emoticon
# emoticon the emoticons come from https://en.wikipedia.org/wiki/List_of_emoticons
# metacharacters: . ^ $ * + ? { } [ ] \ | ( )
HAPPY_RE = r"(:-\)\)|:\)|:D|:o\)|:\]|:3|:c\)|:>|=\]|8\)|=\)|:\}|:\^\)|:-\))" # :-) :) :D :o) :] :3 :c) :> =] 8) =) :} :^) :-))
HAPPY_SUB =r'happy'
LAUGH_RE = r"(:-D|8-D|8D|x-D|xD|X-D|XD|=-D|=D|=-3|=3|B\^D)" # :-D 8-D 8D x-D xD X-D XD =-D =D =-3 =3 B^D
LAUGH_SUB='laugh'
SAD_RE = r"(>:\[|:-\(|:\(|:-c|:c|:-<|:<|:-\[|:\[|:\{|;\()" # >:[ :-( :( :-c :c :-< :< :-[ :[ :{
SAD_SUB=r'sad'
ANGRY_RE = r"(:-\|\||:@|>:\()" # :-|| :@ >:(
ANGRY_SUB=r'angry'
CRY_RE = r"(:'-\(|:'\()" # :'-( :'(
CRY_SUB=r'cry'
HORROR_RE = r"(D:<|D:|D8|D;|D=|DX|v\.v|D-':)" # D:< D: D8 D; D= DX v.v D-':
HORROR_SUB=r'horror'
SURPRISE_RE = r"(>:O|:-O|:O|:-o|:o|8-0|O_O|o-o|O_o|o_O|o_o|O-O)" # >:O :-O :O :-o :o 8-0 O_O o-o O_o o_O o_o O-O
SURPRISE_SUB=r'surprise'
LOVE_RE = r"(:\*|:-\*|:\^\*|\( '}{' \))" # :* :-* :^* ( '}{' )
LOVE_SUB=r'love'
WINK_RE = r"(;-\)|;\)|\*-\)|\*\)|;-\]|;\]|;D|;\^\)|:-,)" # ;-) ;) *-) *) ;-] ;] ;D ;^) :-,
WINK_SUB=r'wink'
UNEASY_RE = r"(>:\\|>:/|:-/|:-\.|:/|:\\|=/|=\\|:L|=L|:S|>\.<)" # >:\ >:/ :-/ :-. :/ :\ =/ =\ :L =L :S >.<
UNEASY_SUB=r'uneasy'
TROUBLE_RE = r"(\(>_<\)|\(>_<\)>)" # (>_<) (>_<)>
TROUBLE_SUB=r'trouble'
```

The last question is: How to use these ?
```python
# The pattern classes
def build_inlinepatterns():
    inline_patterns = OrderedDict()
    inline_patterns['strong'] = (STRONG_RE, STRONG_SUB)
    inline_patterns['emphasis'] = (EMPHASIS_RE, EMPHASIS_SUB)
    inline_patterns['automail'] = (AUTOMAIL_RE, AUTOMAIL_SUB)
    inline_patterns['image_link'] = (IMAGE_LINK_RE, IMAGE_LINK_SUB)
    inline_patterns['link'] = (LINK_RE, LINK_SUB)
    inline_patterns['url'] = (URL_RE, URL_SUB)
    inline_patterns['happy']=(HAPPY_RE,HAPPY_SUB)
    inline_patterns['laugh'] = (LAUGH_RE, LAUGH_SUB)
    inline_patterns['angry'] = (ANGRY_RE, ANGRY_SUB)
    inline_patterns['horror'] = (HORROR_RE, HORROR_SUB)
    inline_patterns['sad']=(SAD_RE,SAD_SUB)
    inline_patterns['cry'] = (CRY_RE, CRY_SUB)
    inline_patterns['surprise'] = (SURPRISE_RE, SURPRISE_SUB)
    inline_patterns['love'] = (LOVE_RE, LOVE_SUB)
    inline_patterns['wink']=(WINK_RE,WINK_SUB)
    inline_patterns['uneasy']=(UNEASY_RE,UNEASY_SUB)
    inline_patterns['trouble']=(TROUBLE_RE,TROUBLE_SUB)
    inline_patterns['rept_punc'] = (REPEAT_PUNC_RE,REPEAT_PUNC_SUB)
    inline_patterns['rept_substr'] = (REPEATED_RE, REPEATED_SUB)
    inline_patterns['rept_sp_punc'] = (SPECIAL_PUNC_RE, SPECIAL_PUNC_SUB)
    inline_patterns['rept_word_punc'] = (REPEAT_WORD_RE, REPEAT_WORD_SUB)
    inline_patterns['digits']=(DIGITS_RE,DIGITD_SUB)
    inline_patterns['html'] = (HTML_RE, HTML_SUB)
    inline_patterns['sspace']=(SPECIAL_SPACE_RE,SPECIAL_SPACE_SUB)
    return inline_patterns

class Pattern(object):
    def __init__(self):
        self.pattern=build_inlinepatterns()
    def sub_text(self,sent):
        for key in self.pattern:
            (patt, sub)=self.pattern[key]
            sent=re.sub(patt,sub,sent)
        return sent.strip()

if __name__=="__main__":
    sents='''~~_no/no~no.no_* '''
    print Pattern().sub_text(sents)
```
