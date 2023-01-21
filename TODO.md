# TODOs

-   during qstnr assembly, ensure that

    -   `targets` doesn't include anything else if `"none"` is present.
    -   `values` is not empty in the end (in the schema we have to allow `"type": "null"` for "reset", but then `value_sets` should still yield actual values).
    -   all `values` subkeys are of the same length (JSON Schema unfortunately [doesn't provide any means to do such
        validation](https://stackoverflow.com/questions/54973793/check-that-two-arrays-in-json-have-the-same-size)).
    -   if `values` and `value_sets` are provided, the latter contains `"values"` (we can't do that in the schema since the [`contains`
        keyword](https://json-schema.org/understanding-json-schema/reference/array.html?highlight=contains#contains) was only added with draft 6).
    -   all items in the same `question_block` have the same `value_set` (we could also allow differing sets, but is that actually a use case?)
    -   `iterators` (an array of iterators) are sensible

-   write documentation

    -   general questionnaire principles
        -   all vars that have translatable values *must* also have `values.int` defined (in order for `as_int_vals` target filters to work)
    -   schema restrictions
        -   allowed characters for footnote `id`s are based on the MDN recommendations found in the [`id` attribute
            article](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/id) (starting with numbers is allowed since the markdown processors
            usually prepend the `id`s with a string like `fn-` anyways)
        -   allowed characters for link `id`s ("labels" in Pandoc terminology): because ["link labels are not case
            sensitive"](https://pandoc.org/MANUAL.html#reference-links), only lowercase letters are allowed; `@` is forbidden to prevent clashes with citation
            keys (which take parsing precedence)

-   publish stable release

    -   replace all occurrences of `https://qstnr.rpkg.dev/dev/` with `https://qstnr.rpkg.dev/` in schemas (and elsewhere)
    -   submit to CRAN

-   implement fns to *write* TOML. requires an R package providing support for this since RcppTOML only offers read support. we could write an R package that
    offers [taplo](https://docs.rs/taplo/latest/taplo/) or [toml](https://docs.rs/toml/latest/toml/) crate bindings; relevant resources:

    -   [Publishing R packages with Rust code on CRAN](https://github.com/r-rust/faq)
    -   [rextendr R package](https://extendr.github.io/rextendr/)
    -   [cargo R package](https://github.com/dbdahl/cargo-framework)

## fundamentals

-   make `value_sets` part "directionless" by

    -   [x] always sorting the definitions ascendingly (must be ), allow new sentinel `desc:` in `value_sets`
    -   [ ] implementing `desc:` interpretation
    -   [ ] communicating new requirement/guarantee in doc
    -   [ ] validating new requirement in code!

-   implement "`value_scale` inference" for `value_scale` arrays (error in case of contradicting info, e.g. asc vs. desc values sets)

-   implement some kind of sentinel (like `free_text:`) to signify free text fields? (label would follow)
