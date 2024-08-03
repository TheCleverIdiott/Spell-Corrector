
# Spell Checker

This project is a simple implementation of a spell checker using a frequency-based approach. The spell checker uses a corpus to build a frequency table of words and then suggests corrections for misspelled words based on their probability.

## Features

- **Tokenization**: Convert text to lowercase and tokenize the document into words.
- **Frequency Table**: Create a frequency table of all words in the corpus.
- **Spelling Corrections**: Generate possible spelling corrections that are one or two edits away from the misspelled word.
- **Probability Calculation**: Calculate the probability of a word based on its frequency in the corpus.
- **Spell Check Function**: Suggest the most probable spelling correction for a given word.

## Getting Started

### Prerequisites

Make sure you have Python 3.9 installed on your machine. You can check your Python version with:

```sh
python --version
```

### Installation

1. Clone this repository to your local machine:

```sh
git clone https://github.com/TheCleverIdiott/spell-checker.git
cd spell-checker
```

2. Install the required Python packages:

```sh
pip install -r requirements.txt
```

### Usage

1. Place your text corpus in the `./corpus` directory and name it `Spell_Corpus.txt`.

2. Run the Jupyter notebook to see the spell checker in action:

```sh
jupyter notebook SpellChecker.ipynb
```

3. Use the `spell_check` function to check the spelling of words:

```python
print(spell_check("monney"))
```

### Example

Here is an example of how the spell checker works:

```python
import re
from collections import Counter

# Function to tokenize words
def words(document):
    """Convert text to lower case and tokenize the document"""
    return re.findall(r'\w+', document.lower())

# Create a frequency table of all the words of the document
all_words = Counter(words(open('./corpus/Spell_Corpus.txt').read()))

# Check frequency of a random word, say, 'chair'
print(all_words['chair'])

# Look at top 10 frequent words
print(all_words.most_common(10))

# Function to generate edits that are one edit away from the word
def edits_one(word):
    """Create all edits that are one edit away from `word`."""
    alphabets = 'abcdefghijklmnopqrstuvwxyz'
    splits = [(word[:i], word[i:]) for i in range(len(word) + 1)]
    deletes = [left + right[1:] for left, right in splits if right]
    inserts = [left + c + right for left, right in splits for c in alphabets]
    replaces = [left + c + right[1:] for left, right in splits if right for c in alphabets]
    transposes = [left + right[1] + right[0] + right[2:] for left, right in splits if len(right) > 1]
    return set(deletes + inserts + replaces + transposes)

# Function to generate edits that are two edits away from the word
def edits_two(word):
    """Create all edits that are two edits away from `word`."""
    return (e2 for e1 in edits_one(word) for e2 in edits_one(e1))

# Function to return the subset of words that appear in the frequency table
def known(words):
    """The subset of `words` that appear in the `all_words`."""
    return set(word for word in words if word in all_words)

# Function to generate possible spelling corrections for a word
def possible_corrections(word):
    """Generate possible spelling corrections for word."""
    return (known([word]) or known(edits_one(word)) or known(edits_two(word)) or [word])

# Function to calculate the probability of a word
def prob(word, N=sum(all_words.values())):
    """Probability of `word`: Number of appearances of 'word' / total number of tokens"""
    return all_words[word] / N

# Function to suggest the most probable spelling correction
def spell_check(word):
    """Print the most probable spelling correction for `word` out of all the `possible_corrections`"""
    correct_word = max(possible_corrections(word), key=prob)
    if correct_word != word:
        return "Did you mean " + correct_word + "?"
    else:
        return "Correct spelling."

# Test the spell check
print(spell_check("monney"))
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request.

## License

This project is licensed under the MIT License.

## Acknowledgments

- Inspired by Peter Norvig's [How to Write a Spelling Corrector](https://norvig.com/spell-correct.html).
