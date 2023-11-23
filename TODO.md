# TODOs

-   move most of default config to pkg fokus

-   implement target iterators handling

-   during qstnr assembly, ensure that

    -   `targets` doesn't include anything else if `"none"` is present.

    -   `values` is not empty in the end (the schema neither demands `values` nor `value_scale`).

    -   all `values` subkeys are of the same length (JSON Schema unfortunately [doesn't provide any means to do such
        validation](https://stackoverflow.com/questions/54973793/check-that-two-arrays-in-json-have-the-same-size)).

        `qstnr::unnest_qstnr_vals()` ensures this implicitly and throws a moderately meaningful error in case of a violation à la:

        ```         
        ! In row 575, can't recycle input of size 2 to size 3.
        ```

    -   if `values` and `value_sets` are provided, the latter contains `"values"` (we can't do that in the schema (yet) since the [`contains`
        keyword](https://json-schema.org/understanding-json-schema/reference/array.html?highlight=contains#contains) was only added with draft 6).

    -   `iterators` are sensible. the schema only demands an array of arrays -- technically anything could be in those level-2 arrays...

-   Taplo uses [jsonschema-rs](https://github.com/Stranger6667/jsonschema-rs) under the hood which (as of 2023-11) supports JSON Schema up to Draft 7, so we
    should be able to use more than just Draft 4 features... -> test this!
    
    Furthermore, implementation of the latest drafts [2019-09](https://github.com/Stranger6667/jsonschema-rs/issues/44) and
    [2020-12](https://github.com/Stranger6667/jsonschema-rs/issues/195) is ongoing. Once this is completed (and can be properly used in taplo), we should
    consider porting our schemas to the latest draft iteration.
    

-   write documentation

    -   general questionnaire principles
        -   all vars that have translatable values *must* also have `values.int` defined (in order for `as_int_vals` target filters to work)
    -   schema restrictions, rules etc.
        -   allowed characters for footnote `id`s are based on the MDN recommendations found in the [`id` attribute
            article](https://developer.mozilla.org/en-US/docs/Web/HTML/Global_attributes/id) (starting with numbers is allowed since the markdown processors
            usually prepend the `id`s with a string like `fn-` anyways)
        -   allowed characters for link `id`s ("labels" in Pandoc terminology): `@` is forbidden to prevent clashes with citation keys (which take parsing
            precedence)
        -   iterators defined via the `i` array are referrable in inline R code by appending the iterator index (starting by `1` as it is the convention for
            vectors in R), i.e. the first iterator is `i1`, the second `i2` etc.

-   submit schemas to [JSON Schema Store](https://www.schemastore.org/json/)? cf. https://taplo.tamasfe.dev/configuration/developing-schemas.html#publishing

-   publish stable release

    -   replace all occurrences of `https://qstnr.rpkg.dev/dev/` with `https://qstnr.rpkg.dev/` in schemas (and elsewhere) -\> change default to
        `schema_url(dev = FALSE)` *thanks to our HTTP redirects we can actually do this already!*
    -   submit to CRAN

-   further investigate shortcomings of taplo's schema validation

    e.g. adding a forbidden survey config key `qstnr.introo` doesn't trigger a validation error in taplo (compare with [this online
    validator](https://www.jsonschemavalidator.net/) which properly errors)

-   implement fns to *write* TOML. requires an R package providing support for this since RcppTOML only offers read support. we could write an R package that
    offers [taplo](https://docs.rs/taplo/latest/taplo/) or [toml](https://docs.rs/toml/latest/toml/) crate bindings; relevant resources:

    -   [Publishing R packages with Rust code on CRAN](https://github.com/r-rust/faq)
    -   [rextendr R package](https://extendr.github.io/rextendr/)
    -   [cargo R package](https://github.com/dbdahl/cargo-framework)

-   Have a look at the following R packages and check whether we should take inspiration from them:

    -   [codebook](https://rubenarslan.github.io/codebook/)
    -   [questionr](https://juba.github.io/questionr/)

-   integrate with suitable open-source survey "servers". candidates include:

    - [LimeSurvey 6](https://www.limesurvey.org/blog/product-news/914-limesurvey-6)
      - still sucks massively: PHP app based on LAMP stack; terrible community both in technical and organizational terms; awful dev experience due to basically
        [unusable/undocumented APIs](https://manual.limesurvey.org/index.php?search=API))
      - to sum up: LimeSurvey has design flaws beyond repairability and is not intended to be used by programmers but only targets non-programmatic usage
      
    - [**Formbricks**](https://formbricks.com/)!
      - offers [REST APIs](https://formbricks.com/docs/api/client/overview) incl. the management API's [survey
        sub-API](https://formbricks.com/docs/api/management/surveys) that i.a. offers an [endpoint to create
        surveys](https://formbricks.com/docs/api/management/surveys#create-survey); this might not be enough for our use case, though (create the *whole* survey
        (incl. routing and everything) from data submitted via API)
      - development is very active and fast, but currently Formbricks still lacks a few mandatory features I think -> need to thoroughly investigate/test!
    
    - see this [short overview](https://formbricks.com/blog/best-open-source-survey-software-2023) for 3 more OSS projects (of which none matches what we want
      😐)

Should we build some kind of [formr](https://formr.org/) integration? qstnr's functionality would probably be a perfect fit...

## fundamentals

-   make `value_sets` part "directionless" by

    -   [x] always sorting the definitions ascendingly, allow new sentinel `desc:` in `value_sets`
    -   [x] implementing `desc:` interpretation
    -   [ ] communicating new requirement/guarantee in doc
    -   [ ] validating new requirement in code!

-   implement some kind of sentinel (like `free_text:`) to signify free text fields? (label would follow)

-   should we introduce another item key `values_ptype` (or the like) to specify what we first wanted via `values = "{ val_ptype(type = date, size = 1L) }"`
    (which the schema doesn't allow anymore)? or come up with a better/proper syntax for value *validation*, depending on ptype (to e.g. ensure a date lies in
    between a specific range), i.e. maybe directly use checkmate?
