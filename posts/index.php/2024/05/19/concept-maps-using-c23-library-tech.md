<html><body><div id="outline-container-orga2bfebe" class="outline-2">
<h2 id="orga2bfebe">Abstract</h2>
<div class="outline-text-2" id="text-orga2bfebe">
<p> C++0x Concepts had a feature <code>Concept Maps</code> that allowed a set of functions, types, and template definitions to be associated with a concept and the map to be specialized for types that meet the concept. </p>

<p> This allowed open extension of a concept. </p>

<!-- TEASER_END -->

<p> A definition could be provided that allows an algorithm to operate in terms of the API a concept presents and the map would define how those operations are implemented for a particular type. </p>

<ul class="org-ul">
<li>This is similar to how Haskell's <code>typeclass</code> works.</li>
</ul>


<div class="notes" id="orgfd6f34c">
<p>  </p>

</div>
</div>
<div id="outline-container-org55a2fab" class="outline-3">
<h3 id="org55a2fab">Lost with <code>Concepts-Lite</code></h3>
<div class="outline-text-3" id="text-org55a2fab">
<div class="notes" id="org7de7f73">
<p> The feature was very general, and lost as part of the <code>Concepts-Lite</code> proposal that was eventually adopted. </p>

<p> This loss of a level of indirection means that the APIs for a concept must be implemented by those names for a type, even when those names are not particularly good choices in the natural domain of a type rather than in the domain as a concept. </p>

<p> The proliferation of <code>transform</code> functions for functorial <code>map</code> is such a problem. </p>

<p> It is also a problem when adapting types that are closed for extension or do not permit member functions. </p>

</div>
</div>
</div>
</div>
<div id="outline-container-org1170181" class="outline-2">
<h2 id="org1170181">Why?</h2>
<div class="outline-text-2" id="text-org1170181">
<ul class="org-ul">
<li><p> Don't know if you should </p></li>
<li>Need to know if you could first</li>
</ul>

<div class="notes" id="org00d3ad4">
<p>  </p>

</div>
</div>
<div id="outline-container-orgec4f485" class="outline-3">
<h3 id="orgec4f485">Alternatives</h3>
<div class="outline-text-3" id="text-orgec4f485">
<ul class="org-ul">
<li><p> Virtual Interface </p></li>
<li><p> Adapters </p></li>
<li>Collection of CPOs</li>
</ul>
<div class="notes" id="org1416f27">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org63dbb2f" class="outline-3">
<h3 id="org63dbb2f">Hard to Support</h3>
<div class="outline-text-3" id="text-org63dbb2f">
<div class="notes" id="org0c07886">
<p>  </p>

</div>
</div>
</div>
</div>
<div id="outline-container-orga340186" class="outline-2">
<h2 id="orga340186">Example from C++0x Concepts</h2>
<div class="outline-text-2" id="text-orga340186">
</div>
<div id="outline-container-org28ca89a" class="outline-3">
<h3 id="org28ca89a">Student Record</h3>
<div class="outline-text-3" id="text-org28ca89a">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">class</span> <span class="org-type">student</span> <span class="org-type">record</span> {
<span class="org-keyword">public</span>:
  <span class="org-type">string</span> <span class="org-variable-name">id</span>;
  <span class="org-type">string</span> <span class="org-variable-name">name</span>;
  <span class="org-type">string</span> <span class="org-variable-name">address</span>;
  <span class="org-type">bool</span>   <span class="org-function-name">id_equal</span>(<span class="org-keyword">const</span> <span class="org-type">student</span> <span class="org-type">record</span>&amp;);
  <span class="org-type">bool</span>   <span class="org-function-name">name_equal</span>(<span class="org-keyword">const</span> <span class="org-type">student</span> <span class="org-type">record</span>&amp;);
  <span class="org-type">bool</span>   <span class="org-function-name">address_equal</span>(<span class="org-keyword">const</span> <span class="org-type">student</span> <span class="org-type">record</span>&amp;);
};
</pre>
</div>
<div class="notes" id="org3e8c3d9">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgaaad96e" class="outline-3">
<h3 id="orgaaad96e">Equality Comparable</h3>
<div class="outline-text-3" id="text-orgaaad96e">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-type">concept_map</span> <span class="org-type">EqualityComparable</span><span class="org-variable-name">&lt;student record&gt;</span>{
    <span class="org-type">bool</span> <span class="org-keyword">operator</span><span class="org-variable-name">==</span>(<span class="org-keyword">const</span> <span class="org-type">student</span> <span class="org-type">record</span>&amp; <span class="org-variable-name">a</span>,
                    <span class="org-keyword">const</span> <span class="org-type">student</span> <span class="org-type">record</span>&amp; <span class="org-variable-name">b</span>){
        <span class="org-keyword">return</span> a.id_equal(b);
}
};
</pre>
</div>

<div class="notes" id="orgc2f233f">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgd6557c8" class="outline-3">
<h3 id="orgd6557c8">Allow associated types</h3>
<div class="outline-text-3" id="text-orgd6557c8">
<p> Very useful for pointers </p>

<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-type">concept_map</span> <span class="org-type">BinaryFunction</span><span class="org-variable-name">&lt;int (*)(int, int), int, int&gt;</span>
{
    <span class="org-keyword">typedef</span> <span class="org-type">int</span> <span class="org-type">result_type</span>;
};
</pre>
</div>


<div class="notes" id="orgac26af1">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org5b19f13" class="outline-3">
<h3 id="org5b19f13">Why Didn't We Get Them?</h3>
<div class="outline-text-3" id="text-org5b19f13">
<p> Let's not go there right now. </p>

<div class="notes" id="orgcd8e7be">
<p>  </p>

</div>
</div>
</div>
</div>
<div id="outline-container-org5455e39" class="outline-2">
<h2 id="org5455e39">State of the Art</h2>
<div class="outline-text-2" id="text-org5455e39">
</div>
<div id="outline-container-org2126bdf" class="outline-3">
<h3 id="org2126bdf">Rust Traits</h3>
<div class="outline-text-3" id="text-org2126bdf">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-rust" id="nil"><span class="org-keyword">trait</span> <span class="org-type">PartialEq</span> {
    <span class="org-keyword">fn</span> <span class="org-function-name">eq</span>(<span class="org-rust-ampersand">&amp;</span><span class="org-keyword">self</span>, <span class="org-variable-name">rhs</span>: <span class="org-rust-ampersand">&amp;</span><span class="org-type">Self</span>) -&gt; <span class="org-type">bool</span>;

    <span class="org-keyword">fn</span> <span class="org-function-name">ne</span>(<span class="org-rust-ampersand">&amp;</span><span class="org-keyword">self</span>, <span class="org-variable-name">rhs</span>: <span class="org-rust-ampersand">&amp;</span><span class="org-type">Self</span>) -&gt; <span class="org-type">bool</span> {
        !<span class="org-keyword">self</span>.eq(rhs)
    }
}
</pre>
</div>

<div class="notes" id="org84edbf7">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org0372946" class="outline-3">
<h3 id="org0372946">C++ CPOs</h3>
<div class="outline-text-3" id="text-org0372946">
</div>
<div id="outline-container-org38d8aac" class="outline-4">
<h4 id="org38d8aac">Some Concepts and Types</h4>
<div class="outline-text-4" id="text-org38d8aac">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">namespace</span> <span class="org-constant">N</span>::<span class="org-constant">hidden</span> {
<span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">concept</span> <span class="org-type">has_eq</span> = <span class="org-keyword">requires</span>(<span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">v</span>) {
  { eq(v, v) } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">bool</span>&gt;;
};

<span class="org-keyword">struct</span> <span class="org-type">eq_fn</span> {
  <span class="org-keyword">template</span> &lt;<span class="org-type">has_eq</span> <span class="org-type">T</span>&gt;
  <span class="org-keyword">constexpr</span> <span class="org-type">bool</span> <span class="org-keyword">operator</span><span class="org-function-name">()</span>(<span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">x</span>,
                            <span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">y</span>) <span class="org-keyword">const</span> {
    <span class="org-keyword">return</span> eq(x, y);
  }
};

<span class="org-keyword">template</span> &lt;<span class="org-type">has_eq</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">constexpr</span> <span class="org-type">bool</span> <span class="org-function-name">ne</span>(<span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">x</span>, <span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">y</span>) {
  <span class="org-keyword">return</span> <span class="org-keyword">not</span> eq(x, y);
}

<span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">concept</span> <span class="org-type">has_ne</span> = <span class="org-keyword">requires</span>(<span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">v</span>) {
  { ne(v, v) } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">bool</span>&gt;;
};

<span class="org-keyword">struct</span> <span class="org-type">ne_fn</span> {
  <span class="org-keyword">template</span> &lt;<span class="org-type">has_ne</span> <span class="org-type">T</span>&gt;
  <span class="org-keyword">constexpr</span> <span class="org-type">bool</span> <span class="org-keyword">operator</span><span class="org-function-name">()</span>(<span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">x</span>,
                            <span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">y</span>) <span class="org-keyword">const</span> {
    <span class="org-keyword">return</span> ne(x, y);
  }
};
} <span class="org-comment-delimiter">// </span><span class="org-comment">namespace N::hidden</span>
</pre>
</div>

<p> See <u>Why tag_invoke is not the solution I want</u> by Barry Revzin <a href="https://brevzin.github.io/c++/2020/12/01/tag-invoke/">https://brevzin.github.io/c++/2020/12/01/tag-invoke/</a> </p>

<div class="notes" id="org4ec3d03">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgd9440f0" class="outline-4">
<h4 id="orgd9440f0">C++ partial_equality</h4>
<div class="outline-text-4" id="text-orgd9440f0">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">namespace</span> <span class="org-constant">N</span> {
<span class="org-keyword">inline</span> <span class="org-keyword">namespace</span> <span class="org-constant">function_objects</span> {
<span class="org-keyword">inline</span> <span class="org-keyword">constexpr</span> <span class="org-constant">hidden</span>::<span class="org-type">eq_fn</span> <span class="org-variable-name">eq</span>{};
<span class="org-keyword">inline</span> <span class="org-keyword">constexpr</span> <span class="org-constant">hidden</span>::<span class="org-type">ne_fn</span> <span class="org-variable-name">ne</span>{};
} <span class="org-comment-delimiter">// </span><span class="org-comment">namespace function_objects</span>

<span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">concept</span> partial_equality
  <span class="org-keyword">requires</span>(<span class="org-constant">std</span>::<span class="org-type">remove_reference_t</span>&lt;<span class="org-type">T</span>&gt; <span class="org-keyword">const</span>&amp; <span class="org-variable-name">t</span>)
{
  eq(t, t);
  ne(t, t);
};
} <span class="org-comment-delimiter">// </span><span class="org-comment">namespace N</span>
</pre>
</div>
<p> See <u>Why tag_invoke is not the solution I want</u> by Barry Revzin <a href="https://brevzin.github.io/c++/2020/12/01/tag-invoke/">https://brevzin.github.io/c++/2020/12/01/tag-invoke/</a> </p>

<div class="notes" id="orgdecd5c0">
<p>  </p>

</div>
</div>
</div>
</div>
</div>
<div id="outline-container-org5396b8f" class="outline-2">
<h2 id="org5396b8f">Requirements for Solution</h2>
<div class="outline-text-2" id="text-org5396b8f">
<ul class="org-ul">
<li><p> Tied to the type system </p></li>
<li><p> Automatable </p></li>
<li>"zero" overhead

<ul class="org-ul">
<li><p> no virtual calls </p></li>
<li>no type erasure</li>
</ul></li>
</ul>

<div class="notes" id="orgc4034ba">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgad4da33" class="outline-2">
<h2 id="orgad4da33">What does typeclass do?</h2>
<div class="outline-text-2" id="text-orgad4da33">
<p> Adds a record to the function that defines the operations for the type. </p>

<p> Can we do that? </p>

<div class="notes" id="org585f6ee">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org3f6e803" class="outline-2">
<h2 id="org3f6e803">Type-based lookup</h2>
<div class="outline-text-2" id="text-org3f6e803">
<p> Templates! </p>

<div class="notes" id="org887d83a">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgcf837b9" class="outline-2">
<h2 id="orgcf837b9">Additional Requirements</h2>
<div class="outline-text-2" id="text-orgcf837b9">
<p> Avoid ADL </p>

<p> Object Lookup rather than Overload Lookup </p>

<div class="notes" id="orgc8f05ae">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org43e91b6" class="outline-2">
<h2 id="org43e91b6">Variable templates</h2>
<div class="outline-text-2" id="text-org43e91b6">
<p> Variable templates have become more powerful </p>

<p> We can have entirely distinct specializations </p>

<div class="notes" id="org9184e90">
<p>  </p>

</div>
</div>
<div id="outline-container-org9b64424" class="outline-3">
<h3 id="org9b64424">A Step Towards Implementation</h3>
<div class="outline-text-3" id="text-org9b64424">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">template</span> &lt;<span class="org-keyword">class</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">concept</span> <span class="org-type">partial_equality</span> = <span class="org-keyword">requires</span>(
    <span class="org-constant">std</span>::<span class="org-type">remove_reference_t</span>&lt;<span class="org-type">T</span>&gt; <span class="org-keyword">const</span>&amp; <span class="org-variable-name">t</span>) {
  {
    <span class="org-type">partial_eq</span>&lt;<span class="org-type">T</span>&gt;.eq(t, t)
  } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">bool</span>&gt;;
  {
    <span class="org-type">partial_eq</span>&lt;<span class="org-type">T</span>&gt;.ne(t, t)
  } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">bool</span>&gt;;
};
</pre>
</div>

<div class="notes" id="org679feb5">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgf24cbc4" class="outline-3">
<h3 id="orgf24cbc4"><code>partial_eq&lt;T&gt;</code></h3>
<div class="outline-text-3" id="text-orgf24cbc4">
</div>
<div id="outline-container-orga7f035c" class="outline-4">
<h4 id="orga7f035c">An inline variable object</h4>
<div class="outline-text-4" id="text-orga7f035c">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">template</span>&lt;<span class="org-keyword">class</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">constexpr</span> <span class="org-keyword">inline</span> <span class="org-keyword">auto</span> <span class="org-variable-name">partial_eq</span> = <span class="org-constant">hidden</span>::partial_eq_default;
</pre>
</div>

<div class="notes" id="org7617290">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orgd69f366" class="outline-4">
<h4 id="orgd69f366">A default implementation</h4>
<div class="outline-text-4" id="text-orgd69f366">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">constexpr</span> <span class="org-keyword">inline</span> <span class="org-keyword">struct</span> <span class="org-type">partial_eq_default_t</span> {
  <span class="org-keyword">constexpr</span> <span class="org-type">bool</span>
  <span class="org-function-name">eq</span>(<span class="org-type">has_eq</span> <span class="org-keyword">auto</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">rhs</span>,
     <span class="org-type">has_eq</span> <span class="org-keyword">auto</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">lhs</span>) <span class="org-keyword">const</span> {
    <span class="org-keyword">return</span> (rhs == lhs);
  }
  <span class="org-keyword">constexpr</span> <span class="org-type">bool</span>
  <span class="org-function-name">ne</span>(<span class="org-type">has_eq</span> <span class="org-keyword">auto</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">rhs</span>,
     <span class="org-type">has_eq</span> <span class="org-keyword">auto</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">lhs</span>) <span class="org-keyword">const</span> {
    <span class="org-keyword">return</span> (lhs != rhs);
  }
} <span class="org-variable-name">partial_eq_default</span>;
</pre>
</div>

<div class="notes" id="org73e4b81">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org07152f2" class="outline-4">
<h4 id="org07152f2">New <code>has_eq</code></h4>
<div class="outline-text-4" id="text-org07152f2">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">T</span>&gt;
<span class="org-keyword">concept</span> <span class="org-type">has_eq</span> = <span class="org-keyword">requires</span>(<span class="org-type">T</span> <span class="org-keyword">const</span>&amp; <span class="org-variable-name">v</span>) {
  { <span class="org-keyword">operator</span>==(v, v) } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">bool</span>&gt;;
};
</pre>
</div>
<div class="notes" id="org40519d3">
<p>  </p>

</div>
</div>
</div>
</div>
<div id="outline-container-org33fa324" class="outline-3">
<h3 id="org33fa324">Will do better</h3>
<div class="outline-text-3" id="text-org33fa324">
<p> In a bit </p>

<div class="notes" id="orgd6431cd">
<p>  </p>

</div>
</div>
</div>
</div>
<div id="outline-container-org8851fb9" class="outline-2">
<h2 id="org8851fb9">Monoid</h2>
<div class="outline-text-2" id="text-org8851fb9">
<p> A little more than you think. </p>

<ul class="org-ul">
<li>A type</li>
<li>With an associative binary operation</li>
<li>Which is closed</li>
<li>And has an identity element</li>
</ul>
</div>
<div id="outline-container-orgb51a0c4" class="outline-3">
<h3 id="orgb51a0c4">Maybe not a lot more</h3>
<div class="outline-text-3" id="text-orgb51a0c4">
<div class="notes" id="org0c44627">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org38e75f7" class="outline-3">
<h3 id="org38e75f7">Math</h3>
<div class="outline-text-3" id="text-org38e75f7">
<ul class="org-ul">
<li>\(\oplus: M \times M \rightarrow M\)</li>
<li>\(x \oplus (y \oplus z) = (x \oplus y) \oplus z\)</li>
<li>\(1_M \in M\) such that \(\forall m \in M : (1_M \oplus m) = m = (m \oplus 1_M)\)</li>
</ul>

<div class="notes" id="org17705e7">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org38a2fc2" class="outline-3">
<h3 id="org38a2fc2">Function form</h3>
<div class="outline-text-3" id="text-org38a2fc2">
<ul class="org-ul">
<li>\(f : M \times M \rightarrow M\)</li>
<li>\(f(x, f(y, z)) = f(f(x, y), z)\)</li>
<li>\(1_M \in M\) such that \(\forall m \in M : f(1_M, m) = m = f(m, 1_M)\)</li>
</ul>

<p> The similarity to left and right fold is <b>NOT</b> an accident </p>

<div class="notes" id="org87b2c28">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-orge87a585" class="outline-3">
<h3 id="orge87a585">Core Functions</h3>
<div class="outline-text-3" id="text-orge87a585">
<dl class="org-dl">
<dt>\(empty : m\)</dt><dd>\(empty = concat \, []\)</dd>
<dt>\(concat : [m] \rightarrow m\)</dt><dd>\(fold \, append \, empty\)</dd>
<dt>\(append : m \rightarrow m \rightarrow m\)</dt><dd>\(op\)</dd>
</dl>


<p> Note that it's self-referential </p>

<p> This is common </p>

<div class="notes" id="orgbae6f76">
<p>  </p>

</div>
</div>
<div id="outline-container-org8204644" class="outline-4">
<h4 id="org8204644">From Haskell Prelude</h4>
<div class="outline-text-4" id="text-org8204644">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-haskell" id="nil"><span class="org-haskell-keyword">class</span> <span class="org-haskell-type">Semigroup</span> a <span class="org-haskell-operator">=&gt;</span> <span class="org-haskell-type">Monoid</span> a <span class="org-haskell-keyword">where</span>
  mempty <span class="org-haskell-operator">::</span> a
  mempty <span class="org-haskell-operator">=</span> mconcat <span class="org-haskell-constructor">[]</span>

  mappend <span class="org-haskell-operator">::</span> a <span class="org-haskell-operator">-&gt;</span> a <span class="org-haskell-operator">-&gt;</span> a
  mappend <span class="org-haskell-operator">=</span> (<span class="org-haskell-operator">&lt;&gt;</span>)

  mconcat <span class="org-haskell-operator">::</span> [a] <span class="org-haskell-operator">-&gt;</span> a
  mconcat <span class="org-haskell-operator">=</span> foldr mappend mempty
</pre>
</div>

<div class="notes" id="org27faaae">
<p>  </p>

</div>
</div>
</div>
</div>
<div id="outline-container-org58ba1f2" class="outline-3">
<h3 id="org58ba1f2">Minimum Set</h3>
<div class="outline-text-3" id="text-org58ba1f2">
<p> \(empty \, | \, concat\) </p>

<div class="notes" id="orgc143f8b">
<p>  </p>

</div>
</div>
</div>
<div id="outline-container-org7fab3d4" class="outline-3">
<h3 id="org7fab3d4">In C++</h3>
<div class="outline-text-3" id="text-org7fab3d4">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">T</span>, <span class="org-keyword">typename</span> <span class="org-type">M</span>&gt;
<span class="org-keyword">concept</span> <span class="org-type">MonoidRequirements</span> =
    <span class="org-keyword">requires</span>(<span class="org-type">T</span> <span class="org-variable-name">i</span>) {
      { i.identity() } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">M</span>&gt;;
    }
    ||
    <span class="org-keyword">requires</span>(<span class="org-type">T</span> <span class="org-variable-name">i</span>, <span class="org-constant">std</span>::<span class="org-constant">ranges</span>::<span class="org-type">empty_view</span>&lt;<span class="org-type">M</span>&gt; <span class="org-variable-name">r1</span>) {
      { i.concat(r1) } -&gt; <span class="org-constant">std</span>::<span class="org-type">same_as</span>&lt;<span class="org-type">M</span>&gt;;
    };
</pre>
</div>
<div class="notes" id="org2c61343">
<p> I am ignoring all sorts of const volatile reference issues here. </p>

</div>
</div>
</div>
</div>
<div id="outline-container-org55db03e" class="outline-2">
<h2 id="org55db03e">Implementing the other side</h2>
<div class="outline-text-2" id="text-org55db03e">
</div>
<div id="outline-container-org94ca958" class="outline-3">
<h3 id="org94ca958">The Map for a Monoid</h3>
<div class="outline-text-3" id="text-org94ca958">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">template</span> &lt;<span class="org-keyword">class</span> <span class="org-type">Impl</span>&gt;
  <span class="org-keyword">requires</span> <span class="org-type">MonoidRequirements</span>&lt;
      <span class="org-type">Impl</span>,
      <span class="org-keyword">typename</span> <span class="org-constant">Impl</span>::<span class="org-type">value_type</span>&gt;
<span class="org-keyword">struct</span> <span class="org-type">Monoid</span> : <span class="org-keyword">protected</span> <span class="org-type">Impl</span> {
  <span class="org-keyword">auto</span> <span class="org-variable-name">identity</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self);

  <span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">Range</span>&gt;
  <span class="org-keyword">auto</span> <span class="org-variable-name">concat</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self, <span class="org-type">Range</span> <span class="org-variable-name">r</span>);

  <span class="org-keyword">auto</span> <span class="org-variable-name">op</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self, <span class="org-keyword">auto</span> <span class="org-variable-name">a1</span>, <span class="org-keyword">auto</span> <span class="org-variable-name">a2</span>);
};
</pre>
</div>
<div class="notes" id="org2cb36b8">
<p> empty is a terrible name, concat only a little better. empty becomes identity </p>

</div>
</div>
<div id="outline-container-orgf2e4e66" class="outline-4">
<h4 id="orgf2e4e66"><code>identity</code></h4>
<div class="outline-text-4" id="text-orgf2e4e66">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil">    <span class="org-keyword">auto</span> <span class="org-variable-name">identity</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span> &amp;&amp; self) {
        <span class="org-constant">std</span>::puts(<span class="org-string">"Monoid::identity()"</span>);
        <span class="org-keyword">return</span> self.concat(<span class="org-constant">std</span>::<span class="org-constant">ranges</span>::<span class="org-type">empty_view</span>&lt;<span class="org-keyword">typename</span> <span class="org-constant">Impl</span>::<span class="org-type">value_type</span>&gt;{});
    }

</pre>
</div>
</div>
</div>
<div id="outline-container-org4544cc8" class="outline-4">
<h4 id="org4544cc8"><code>concat</code></h4>
<div class="outline-text-4" id="text-org4544cc8">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil">   <span class="org-keyword">template</span>&lt;<span class="org-keyword">typename</span> <span class="org-type">Range</span>&gt;
   <span class="org-keyword">auto</span> <span class="org-variable-name">concat</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self, <span class="org-type">Range</span> <span class="org-variable-name">r</span>) {
        <span class="org-constant">std</span>::puts(<span class="org-string">"Monoid::concat()"</span>);
        <span class="org-keyword">return</span> <span class="org-constant">std</span>::<span class="org-constant">ranges</span>::fold_right(r,
                    self.identity(),
                    [&amp;](<span class="org-keyword">auto</span> <span class="org-variable-name">m1</span>, <span class="org-keyword">auto</span> <span class="org-variable-name">m2</span>){<span class="org-keyword">return</span> self.op(m1, m2);});
    }

</pre>
</div>
</div>
</div>
<div id="outline-container-org7fb7987" class="outline-4">
<h4 id="org7fb7987"><code>op</code></h4>
<div class="outline-text-4" id="text-org7fb7987">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil">   <span class="org-keyword">auto</span> <span class="org-variable-name">op</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self, <span class="org-keyword">auto</span> <span class="org-variable-name">a1</span>, <span class="org-keyword">auto</span> <span class="org-variable-name">a2</span>) {
        <span class="org-constant">std</span>::puts(<span class="org-string">"Monoid::op"</span>);
        <span class="org-keyword">return</span> self.op(a1, a2);
    }
</pre>
</div>
</div>
</div>
</div>
<div id="outline-container-org433bfeb" class="outline-3">
<h3 id="org433bfeb">Deducing <code>this</code> <b>and</b> CRTP</h3>
<div class="outline-text-3" id="text-org433bfeb">
<p> We'll see in a moment, but it's because we want to constraint the required implementation. </p>

<p> We want to use the derived version which has all of the operations. </p>
</div>
</div>
<div id="outline-container-orgd365e33" class="outline-3">
<h3 id="orgd365e33"><code>Plus</code></h3>
<div class="outline-text-3" id="text-orgd365e33">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">M</span>&gt;
<span class="org-keyword">class</span> <span class="org-type">Plus</span> {
<span class="org-keyword">public</span>:
  <span class="org-keyword">using</span> <span class="org-type">value_type</span> = M;
  <span class="org-keyword">auto</span> <span class="org-variable-name">identity</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self) -&gt; <span class="org-type">M</span> {
    <span class="org-constant">std</span>::puts(<span class="org-string">"Plus::identity()"</span>);
    <span class="org-keyword">return</span> M{0};
  }

  <span class="org-keyword">auto</span> <span class="org-variable-name">op</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self, <span class="org-keyword">auto</span> <span class="org-variable-name">s1</span>, <span class="org-keyword">auto</span> <span class="org-variable-name">s2</span>) -&gt; <span class="org-type">M</span> {
    <span class="org-constant">std</span>::puts(<span class="org-string">"Plus::op()"</span>);
    <span class="org-keyword">return</span> s1 + s2;
  }
};
</pre>
</div>
</div>
</div>
<div id="outline-container-org0a4f2c7" class="outline-3">
<h3 id="org0a4f2c7"><code>PlusMonoidMap</code></h3>
<div class="outline-text-3" id="text-org0a4f2c7">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">template</span>&lt;<span class="org-keyword">typename</span> <span class="org-type">M</span>&gt;
<span class="org-keyword">struct</span> <span class="org-type">PlusMonoidMap</span> : <span class="org-keyword">public</span> <span class="org-type">Monoid</span>&lt;<span class="org-type">Plus</span>&lt;<span class="org-type">M</span>&gt;&gt; {
    <span class="org-keyword">using</span> <span class="org-constant">Plus</span>&lt;<span class="org-type">M</span>&gt;::identity;
    <span class="org-keyword">using</span> <span class="org-constant">Plus</span>&lt;<span class="org-type">M</span>&gt;::op;
};
</pre>
</div>
<div class="notes" id="orga6a3f61">
<p> Need to pull the operations from the Monoid instance into the Map, so we get the right ones being used by concat. </p>

<p> This might be simpler if we didn't allow choice of the basis operations, but that's also overly restrictive. </p>

</div>
</div>
</div>
<div id="outline-container-orgc757316" class="outline-3">
<h3 id="orgc757316">The map instances</h3>
<div class="outline-text-3" id="text-orgc757316">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">template</span>&lt;<span class="org-keyword">class</span> <span class="org-type">T</span>&gt; <span class="org-keyword">auto</span> <span class="org-variable-name">monoid_concept_map</span> = <span class="org-constant">std</span>::false_type{};

<span class="org-keyword">template</span>&lt;&gt;
<span class="org-keyword">constexpr</span> <span class="org-keyword">inline</span> <span class="org-keyword">auto</span> <span class="org-type">monoid_concept_map</span><span class="org-variable-name">&lt;int&gt;</span> = <span class="org-type">PlusMonoidMap</span>&lt;<span class="org-type">int</span>&gt;{};

<span class="org-keyword">template</span>&lt;&gt;
<span class="org-keyword">constexpr</span> <span class="org-keyword">inline</span> <span class="org-keyword">auto</span> <span class="org-type">monoid_concept_map</span><span class="org-variable-name">&lt;long&gt;</span> = <span class="org-type">PlusMonoidMap</span>&lt;<span class="org-type">long</span>&gt;{};

<span class="org-keyword">template</span>&lt;&gt;
<span class="org-keyword">constexpr</span> <span class="org-keyword">inline</span> <span class="org-keyword">auto</span> <span class="org-type">monoid_concept_map</span><span class="org-variable-name">&lt;char&gt;</span> = <span class="org-type">PlusMonoidMap</span>&lt;<span class="org-type">char</span>&gt;{};
</pre>
</div>
</div>
</div>
<div id="outline-container-org4a87cf1" class="outline-3">
<h3 id="org4a87cf1">Can we <code>concat</code> instead?</h3>
<div class="outline-text-3" id="text-org4a87cf1">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">class</span> <span class="org-type">StringMonoid</span> {
<span class="org-keyword">public</span>:
  <span class="org-keyword">using</span> <span class="org-type">value_type</span> = <span class="org-constant">std</span>::string;

  <span class="org-keyword">auto</span> <span class="org-variable-name">op</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp;, <span class="org-keyword">auto</span> <span class="org-variable-name">s1</span>, <span class="org-keyword">auto</span> <span class="org-variable-name">s2</span>) {
    <span class="org-constant">std</span>::puts(<span class="org-string">"StringMonoid::op()"</span>);
    <span class="org-keyword">return</span> s1 + s2;
  }

  <span class="org-keyword">template</span> &lt;<span class="org-keyword">typename</span> <span class="org-type">Range</span>&gt;
  <span class="org-keyword">auto</span> <span class="org-variable-name">concat</span>(<span class="org-keyword">this</span> <span class="org-keyword">auto</span>&amp;&amp; self, <span class="org-type">Range</span> <span class="org-variable-name">r</span>) {
    <span class="org-constant">std</span>::puts(<span class="org-string">"StringMonoid::concat()"</span>);
    <span class="org-keyword">return</span> <span class="org-constant">std</span>::<span class="org-constant">ranges</span>::fold_right(
        r, <span class="org-constant">std</span>::string{}, [&amp;](<span class="org-keyword">auto</span> <span class="org-variable-name">m1</span>, <span class="org-keyword">auto</span> <span class="org-variable-name">m2</span>) {
          <span class="org-keyword">return</span> self.op(m1, m2);
        });
  }
};
</pre>
</div>
<div class="notes" id="org1421b14">
<p> No, I'm not properly constraining Range here. No, I'm not actually recommending this as an implementation. </p>

</div>
</div>
</div>
<div id="outline-container-org3c488f3" class="outline-3">
<h3 id="org3c488f3">The Map and instance</h3>
<div class="outline-text-3" id="text-org3c488f3">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil"><span class="org-keyword">struct</span> <span class="org-type">StringMonoidMap</span> : <span class="org-keyword">public</span> <span class="org-type">Monoid</span>&lt;StringMonoid&gt; {
    <span class="org-keyword">using</span> <span class="org-constant">StringMonoid</span>::op;
    <span class="org-keyword">using</span> <span class="org-constant">StringMonoid</span>::concat;
};

<span class="org-keyword">template</span>&lt;&gt;
<span class="org-keyword">constexpr</span> <span class="org-keyword">inline</span> <span class="org-keyword">auto</span> <span class="org-type">monoid_concept_map</span>&lt;<span class="org-constant">std</span>::<span class="org-variable-name">string&gt;</span> = StringMonoidMap{};

</pre>
</div>
</div>
</div>
</div>
<div id="outline-container-org3a12eef" class="outline-2">
<h2 id="org3a12eef">Some simple use</h2>
<div class="outline-text-2" id="text-org3a12eef">
</div>
<div id="outline-container-orgd1b5091" class="outline-3">
<h3 id="orgd1b5091">Exercise the functions</h3>
<div class="outline-text-3" id="text-orgd1b5091">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil"><span class="org-keyword">template</span>&lt;<span class="org-keyword">typename</span> <span class="org-type">P</span>&gt;
<span class="org-type">void</span> <span class="org-function-name">testP</span>()
{
    <span class="org-keyword">auto</span> <span class="org-variable-name">d1</span> = <span class="org-type">monoid_concept_map</span>&lt;<span class="org-type">P</span>&gt;;

    <span class="org-keyword">auto</span> <span class="org-variable-name">x</span> = d1.identity();
    assert(P{} == x);

    <span class="org-keyword">auto</span> <span class="org-variable-name">sum</span> = d1.op(x, P{1});
    assert(P{1} == sum);

    <span class="org-constant">std</span>::<span class="org-type">vector</span>&lt;<span class="org-type">P</span>&gt; <span class="org-variable-name">v</span> = {1,2,3,4};
    <span class="org-keyword">auto</span> <span class="org-variable-name">k</span> = d1.concat(v);
    assert(k == 10);
}
</pre>
</div>
</div>
</div>
<div id="outline-container-orgd13d04e" class="outline-3">
<h3 id="orgd13d04e">Some simple cases</h3>
<div class="outline-text-3" id="text-orgd13d04e">
<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-c++" id="nil">    <span class="org-constant">std</span>::cout &lt;&lt; <span class="org-string">"\ntest int\n"</span>;
    testP&lt;<span class="org-type">int</span>&gt;();

    <span class="org-constant">std</span>::cout &lt;&lt; <span class="org-string">"\ntest long\n"</span>;
    testP&lt;<span class="org-type">long</span>&gt;();

   <span class="org-constant">std</span>::cout &lt;&lt; <span class="org-string">"\ntest char\n"</span>;
    testP&lt;<span class="org-type">char</span>&gt;();

</pre>
</div>
</div>
</div>
<div id="outline-container-orga5b5669" class="outline-3">
<h3 id="orga5b5669">On <code>std::string</code></h3>
<div class="outline-text-3" id="text-orga5b5669">
<p> This will use the StringMonoid we defined a few moments ago. </p>

<div class="org-src-container">
<label class="org-src-name"><em></em></label>
<pre class="src src-C++" id="nil">    <span class="org-keyword">auto</span> <span class="org-variable-name">d2</span> = <span class="org-type">monoid_concept_map</span>&lt;<span class="org-constant">std</span>::string&gt;;

    <span class="org-constant">std</span>::cout &lt;&lt; <span class="org-string">"\ntest string\n"</span>;
    <span class="org-keyword">auto</span> <span class="org-variable-name">x2</span> = d2.identity();
    assert(<span class="org-constant">std</span>::string{} == x2);

    <span class="org-keyword">auto</span> <span class="org-variable-name">sum2</span> = d2.op(x2, <span class="org-string">"1"</span>);
    assert(<span class="org-constant">std</span>::string{<span class="org-string">"1"</span>} == sum2);

    <span class="org-constant">std</span>::<span class="org-type">vector</span>&lt;<span class="org-constant">std</span>::string&gt; <span class="org-variable-name">vs</span> = {<span class="org-string">"1"</span>,<span class="org-string">"2"</span>,<span class="org-string">"3"</span>,<span class="org-string">"4"</span>};
    <span class="org-keyword">auto</span> <span class="org-variable-name">k2</span> = d2.concat(vs);
    assert(k2 == <span class="org-constant">std</span>::string{<span class="org-string">"1234"</span>});
</pre>
</div>

<p> Note that the map type is mostly invisible. </p>
</div>
</div>
<div id="outline-container-orgb719e3f" class="outline-3">
<h3 id="orgb719e3f">Results</h3>
<div class="outline-text-3" id="text-orgb719e3f">
</div>
<div id="outline-container-org24e555f" class="outline-4">
<h4 id="org24e555f">test int</h4>
<div class="outline-text-4" id="text-org24e555f">
<em></em>
<pre class="example" id="nil">
Plus::identity()
Plus::op()
Monoid::concat()
Plus::identity()
Plus::op()
Plus::op()
Plus::op()
Plus::op()
</pre>
</div>
</div>
<div id="outline-container-orge66a8b5" class="outline-4">
<h4 id="orge66a8b5">test long</h4>
<div class="outline-text-4" id="text-orge66a8b5">
<em></em>
<pre class="example" id="nil">
Plus::identity()
Plus::op()
Monoid::concat()
Plus::identity()
Plus::op()
Plus::op()
Plus::op()
Plus::op()
</pre>
</div>
</div>
<div id="outline-container-orgf7ae047" class="outline-4">
<h4 id="orgf7ae047">test char</h4>
<div class="outline-text-4" id="text-orgf7ae047">
<em></em>
<pre class="example" id="nil">
Plus::identity()
Plus::op()
Monoid::concat()
Plus::identity()
Plus::op()
Plus::op()
Plus::op()
Plus::op()
</pre>
</div>
</div>
<div id="outline-container-org7390f2c" class="outline-4">
<h4 id="org7390f2c">test string</h4>
<div class="outline-text-4" id="text-org7390f2c">
<em></em>
<pre class="example" id="nil">
Monoid::identity()
StringMonoid::concat()
StringMonoid::op()
StringMonoid::concat()
StringMonoid::op()
StringMonoid::op()
StringMonoid::op()
StringMonoid::op()
</pre>
</div>
</div>
</div>
</div>
<div id="outline-container-orga5cfe90" class="outline-2">
<h2 id="orga5cfe90">Monoid in Trees</h2>
<div class="outline-text-2" id="text-orga5cfe90">
</div>
<div id="outline-container-org8d2fc90" class="outline-3">
<h3 id="org8d2fc90">Foldable generalizes</h3>
<div class="outline-text-3" id="text-org8d2fc90">
<p> Folding is very much tied to Range like things. </p>

<p> It can, and has, been generalized to things that can be traversed. </p>

<p> <code>monoids</code> are still critical for Traversables. </p>
</div>
</div>
<div id="outline-container-org5737e19" class="outline-3">
<h3 id="org5737e19">Summarizing Data in a tree</h3>
<div class="outline-text-3" id="text-org5737e19">
<p> If the summary type is monoidal, nodes can hold summaries of all the data below them. </p>
</div>
</div>
<div id="outline-container-org20d51eb" class="outline-3">
<h3 id="org20d51eb"><code>fingertrees</code></h3>
<div class="outline-text-3" id="text-org20d51eb">
<p> Much of the flexibility of <code>fingertrees</code> comes from the monoidal tags. </p>

<p> They are also fairly complicated. </p>

<p> Technique can be applied to other, simpler trees. </p>

<p> P3200 (eventually) ((C++29)) </p>
</div>
</div>
<div id="outline-container-orgeea587e" class="outline-3">
<h3 id="orgeea587e">fringe-tree</h3>
<div class="outline-text-3" id="text-orgeea587e">
<p> Simplified tree with data at the edges </p>
</div>
</div>
<div id="outline-container-orgacbba9a" class="outline-3">
<h3 id="orgacbba9a">Code</h3>
<div class="outline-text-3" id="text-orgacbba9a">
<p> Show the monoid-map branch of </p>

<p> <a href="https://github.com/steve-downey/fringetree">steve-downey/fringetree.git</a> </p>
</div>
</div>
</div>
<div id="outline-container-orgab7d8a2" class="outline-2">
<h2 id="orgab7d8a2">Summary for Concept Maps</h2>
<div class="outline-text-2" id="text-orgab7d8a2">
<p> Tell you what I told you </p>

<ul class="org-ul">
<li>Variable templates for map lookup</li>
<li>Named operations on the map object</li>
<li>Open for extension</li>
<li>Concept checkable implementations</li>
<li>Decoupled map use and implementation</li>
</ul>
</div>
</div>
<div id="outline-container-org8395959" class="outline-2">
<h2 id="org8395959">Questions?</h2>
<div class="outline-text-2" id="text-org8395959">
<p> Or comments </p>
</div>
</div>
<div id="outline-container-org3983c15" class="outline-2">
<h2 id="org3983c15">Thank You</h2>
<div class="outline-text-2" id="text-org3983c15">
<div class="notes" id="org4db291f">
<p>  </p>

</div>
</div>
</div>
</body></html>