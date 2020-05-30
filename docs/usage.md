---
template: overrides/main.html
---

# Usage

## fuzz
### ratio

Calculates a simple ratio between two strings.

=== "Python"

    Parameters:

    - **s1**: *str*

        First string to compare.

    - **s2**: *str*

        Second string to compare.

    - **processor**: *(Union[bool, Callable])*, default `None`

        Optional callable that reformats the strings.

        - `None` is use by default in `fuzz.ratio` and `fuzz.partial_ratio`.
        - `utils.default_process`, which lowercases the strings and trims whitespaces, is used by default for all other `fuzz.*ratio` methods

    - **score_cutoff**: *float*, default `0`, optional

        Optional argument for a score threshold as a float between 0 and 100. For `ratio < score_cutoff`, 0 is returned instead.

    Returns:

    - **score**: *float*

        Ratio between `s1` and `s2` as a float between 0 and 100


    ```bash
    > from rapidfuzz import fuzz
    > fuzz.ratio("this is a test", "this is a test!")
    96.55171966552734
    ```

=== "C++"
    ```cpp
    #include "fuzz.hpp"
    using rapidfuzz::fuzz::ratio;

    // score is 96.55171966552734
    double score = rapidfuzz::fuzz::ratio("this is a test", "this is a test!");
    ```


### partial_ratio

Calculates the [ratio](#ratio) of the optimal string alignment

=== "Python"

    Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

    Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.

    ```bash
    > from rapidfuzz import fuzz
    > fuzz.partial_ratio("this is a test", "this is a test!")
    100
    ```

=== "C++"
    ```cpp
    #include "fuzz.hpp"
    using rapidfuzz::fuzz::partial_ratio;

    // score is 100
    double score = rapidfuzz::fuzz::partial_ratio("this is a test", "this is a test!");
    ```

### token_sort_ratio

Sorts the words in the strings and calculates the [ratio](#ratio) between them.

=== "Python"

    Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

    Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.


    ```bash
    > from rapidfuzz import fuzz
    > fuzz.token_sort_ratio("fuzzy wuzzy was a bear", "wuzzy fuzzy was a bear")
    100
    ```

=== "C++"
    ```cpp
    #include "fuzz.hpp"
    using rapidfuzz::fuzz::token_sort_ratio;

    // score is 100
    double score = token_sort_ratio("fuzzy wuzzy was a bear", "wuzzy fuzzy was a bear")
    ```

### partial_token_sort_ratio

Sorts the words in the strings and calculates the [partial_ratio](#partial_ratio) between them.

Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.


### token_set_ratio

Compares the words in the strings based on unique and common words between them using [ratio](#ratio).

=== "Python"
    Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

    Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.

    ```bash
    > fuzz.token_sort_ratio("fuzzy was a bear", "fuzzy fuzzy was a bear")
    83.8709716796875
    > fuzz.token_set_ratio("fuzzy was a bear", "fuzzy fuzzy was a bear")
    100.0
    ```

=== "C++"
    ```cpp
    #include "fuzz.hpp"
    using rapidfuzz::fuzz::token_sort_ratio;
	using rapidfuzz::fuzz::token_set_ratio;

    // score1 is 83.87
    double score1 = token_sort_ratio("fuzzy was a bear", "fuzzy fuzzy was a bear")
    // score2 is 100
    double score2 = token_set_ratio("fuzzy was a bear", "fuzzy fuzzy was a bear")
    ```

### partial_token_set_ratio

Compares the words in the strings based on unique and common words between them using [partial_ratio](#partial_ratio).

Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.


### token_ratio

Helper method that returns the maximum of [token_set_ratio](#token_set_ratio) and
[token_sort_ratio](#token_sort_ratio) (faster than manually executing the two functions)

Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.


### partial_token_ratio

Helper method that returns the maximum of [partial_token_set_ratio](#partial_token_set_ratio) and
[partial_token_sort_ratio](#partial_token_sort_ratio) (faster than manually executing the two functions)

Parameters: Same as `fuzz.ratio` - `s1`, `s2`, `processor`. See [ratio](#ratio) for further details.

Returns: Same as `fuzz.ratio`. See [ratio](#ratio) for further details.


### QRatio
Similar algorithm to [ratio](#ratio), but preprocesses the strings by default, while it does not do this by default in
[ratio](#ratio).

### WRatio
Calculates a weighted ratio based on the other ratio algorithms.


## process

### extract

Find the best matches in a list of choices.

=== "Python"

    Parameters:

    - **query**: *str*

        String we want to find.

    - **choices**: *Iterable*

        List of all strings the query should be compared with or dict with a mapping
        in the form of `{<result>: <string to compare>}`. Mapping can be anything
        that provides an `items` method like a python `dict` or `pandas.Series` (index: element)

    - **scorer**: *Callable*, default `fuzz.WRatio`

        Optional callable that is used to calculate the matching score between
        the query and each choice.

    - **processor**: *Callable*, default `utils.default_process`

        Optional callable that reformats the strings. `utils.default_process`
        is used by default, which lowercases the strings and trims whitespace

    - **limit**: *int*

        Maximum amount of results to return.

    - **score_cutoff**: *float*, default `0`

        Optional argument for a score threshold. Matches with
        a lower score than this number will not be returned.

    Returns:

    - **matches**: *List[Tuple[str, float]] or List[Tuple[str, float, str]])*

        Returns a list of all matches that have a `score >= score_cutoff`. The list will
        be of either `(, )` when `choices` is a list of strings or `(, , )` when `choices` is a
        mapping.


    ```console
    > choices = ["Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"]
    > process.extract("new york jets", choices, limit=2)
    [('new york jets', 100), ('new york giants', 78.57142639160156)]
    ```

=== "C++"
    ```cpp
    #include "process.hpp"
    using rapidfuzz::process::extract;

    // matches is a vector of std::pairs
    // [('new york jets', 100), ('new york giants', 78.57142639160156)]
    auto matches = extract(
      "new york jets",
      std::vector<std::string>{"Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"},
      utils::default_process<char>,
      fuzz::ratio<std::string, std::string>
      2);
    ```

### extractOne
Finds the best match in a list of choices by comparing them using the provided scorer functions.

=== "Python"

    Parameters: Same as [extract](#extract)

    Returns:

    - **matches**: *Optional[Tuple[str, float]]*

        Returns the best match in form of a tuple or None when there is
        no match with a `score >= score_cutoff`.


    ```console
    > choices = ["Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"]
    > process.extractOne("cowboys", choices)
    ("dallas cowboys", 90)
    ```

=== "C++"
    ```cpp
    #include "process.hpp"
    using rapidfuzz::process::extractOne;

    // matches is a boost::optional<std::pair>
    // ("dallas cowboys", 90)
    auto matches = extractOne(
      "cowboys",
      std::vector<std::string>{"Atlanta Falcons", "New York Jets", "New York Giants", "Dallas Cowboys"});
    ```