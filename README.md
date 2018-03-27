#README#Daniel Schopper, last update 3/27/2018 8:28:01 PM  This is a set of XSLT-Stylesheets to tokenize marked-up text and create a list of tei:w and tei:pc-Elements[1] from it.## What is handled ## Usage### OverviewUsing these Stylesheets consists of the following steps:1. Writing a *Tokenization Profile Definition* which describes the tokenization process in detail.2. Compilation of a set of wrapper stylesheets which essentially translate the profile into a set of XSL parameters and templates.3. Preparation of the source document, removing any non-significant whitespaces.2. Tokenizing the source document and adding links between partial tokens3. Optionally: outputting a so-called vertical as a XML-TEI document and/or a plain text file.Please refer to HOWTO.md for a complete walk-through of the process plus a description of available short-cuts.### Preparation of the SourceThe XML source is expected to consist only of meaningful whitespace in order to work correctly. The Stylesheet @rmNl.xsl@ does normalization on a TEI text, where each @<lb/>@ is followed by a newline (and some other cases). Adjust accordingly to your input document.TODO: Look if this can be replaced by <https://wiki.tei-c.org/index.php/XML_Whitespace#XSLT_Normalization_Code>### The Tokenization Profile DefinitionTokenization of XML structures involves knowledge about the semantics of the markup. For example each project has to decide which tags do not imply a token boundary. To do so, we have to provide a configuration file which provides information about:1. parameters for the tokenization process2. the namespace(s) and prefixes of the markup3. a set of XPath expressions to select elements that are *not* to be tokenized (for example, in many cases @<tei:fw>@-elements which are not part of the running text) - these are to be placed inside the <ignore>-element in the parameter file.4. a set of XPath expressions to identify tags that do not imply word/token boundaries - these are to be placed inside the <in-word-tags>-element in the parameter file.Please see @profiles/default/profile.xml@ for such a parameter file. Processing this parameter file with 'make_xsl.xsl' produces 5 stylesheets under `profiles/default`:* **param.xsl** - the parameter stylesheet containing the XSL parameters and templates defined by the profile definition document. * **wrapper_toks.xsl**: the main stylesheet which will be used for the tokenization process (see step 4), importing params.xsl* **wrapper_addP.xsl**: a stylehseet responsible for adding explicit links and `@p`-attributes to partial tokens.* **wrapper_tei2vert.xsl**: a styleheet that transforms the input document into a so called vertical, i.e. a flat token list (encoded in TEI-XML) only containing selected structure elemeents.* **wrapper_vert2txt.xsl**: a styleheet that transforms the TEI-vertical into a text version wich can be consumed by corpus query engines or NLP applications.TODO: Describe advanced features like token dictionary, whitespace and PC-definition, post-tokenization stylesheet### OutputTokens are tagged with the tei:w-Tag, the strucure and namespace of the source document is not altered. Tokens which are disrupted by markup carry a @@part@-attribute, indicating whether this is the inital (@@part="I"@), middle (@@part="M"@) or final (@@part="F"@) part of the token. Moreover, for ease of processing, explicit links between those parts using @@prev@ and @@next@ are added to each.TODO: add option to output as @@att.linguistic@ attributes.[1] http://www.tei-c.org/release/doc/tei-p5-doc/en/html/ref-w.html