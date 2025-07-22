# gramma-lite

A lightweight JavaScript version of gramma - a grammar and style checker that uses LanguageTool API to provide intelligent writing suggestions.

## Features

- **Grammar & Style Checking**: Detects grammar, spelling, and style issues in your text
- **Multiple Language Support**: Supports various languages through LanguageTool API
- **Customizable Rules**: Enable/disable specific rule categories based on your needs

- **False Positive Filtering**: Includes dictionary support to reduce false positives
- **Lightweight**: Minimal dependencies and footprint

## Installation

```bash
yarn add gramma-lite
```

## Quick Start

```javascript
const grammarChecker = require('gramma-lite');

// Basic usage
const text = "This is a test text with some grammer errors.";
const result = await grammarChecker(text);
console.log(result.matches);
```

## API Reference

### `grammarChecker(text, options)`

Checks the provided text for grammar and style issues.

#### Parameters

- `text` (string): The text to check
- `options` (object, optional): Configuration options

#### Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `api_url` | string | `"https://api.languagetool.org/v2/check"` | LanguageTool API endpoint |
| `api_key` | string | `""` | API key for premium features |
| `language` | string | `"en-US"` | Language code for checking |
| `dictionary` | array | `[]` | Custom dictionary words to ignore |

| `rules` | object | All enabled | Rule category settings |

#### Rule Categories

The following rule categories can be enabled/disabled:

- `CASING` - Detecting uppercase words where lowercase is required and vice versa
- `COLLOQUIALISMS` - Colloquial style
- `COMPOUNDING` - Rules about spelling terms as one word or as separate words
- `CONFUSED_WORDS` - Words that are easily confused, like 'there' and 'their'
- `FALSE_FRIENDS` - Words easily confused by language learners
- `GENDER_NEUTRALITY` - Gender neutrality rules
- `GRAMMAR` - General grammar rules
- `MISC` - Miscellaneous rules that don't fit elsewhere
- `PUNCTUATION` - Punctuation rules
- `REDUNDANCY` - Redundancy detection
- `REGIONALISMS` - Words used only in another language variant
- `REPETITIONS` - Repetition detection
- `SEMANTICS` - Logic, content, and consistency problems
- `STYLE` - General style issues not covered by other categories
- `TYPOGRAPHY` - Problems like incorrectly used dash or quote characters
- `TYPOS` - Spelling issues

## Examples

### Basic Grammar Check

```javascript
const grammarChecker = require('gramma-lite');

async function checkText() {
  const text = "I have went to the store yesterday.";

  try {
    const result = await grammarChecker(text);

    result.matches.forEach(match => {
      console.log(`Issue: ${match.message}`);
      console.log(`Suggestion: ${match.replacements[0]?.value || 'No suggestion'}`);
      console.log(`Context: "${match.context.text}"`);
      console.log('---');
    });
  } catch (error) {
    console.error('Error:', error.message);
  }
}

checkText();
```

### Custom Configuration

```javascript
const grammarChecker = require('gramma-lite');

const options = {
  language: 'en-GB',
  api_key: 'your-api-key-here',
  dictionary: ['customword', 'anotherword'],
  rules: {
    TYPOS: false,        // Disable spelling checks
    STYLE: true,         // Enable style checks
    GRAMMAR: true        // Enable grammar checks
  }
};

const text = "Your text here...";
const result = await grammarChecker(text, options);
```



### Error Handling

```javascript
const grammarChecker = require('gramma-lite');

async function safeCheck(text) {
  try {
    const result = await grammarChecker(text);
    return result;
  } catch (error) {
    if (error.message.includes('Language not available')) {
      console.log('Please check your language setting or install a local LanguageTool server');
    } else {
      console.log('API error:', error.message);
    }
    return null;
  }
}
```

## Response Format

The API returns an object with the following structure:

```javascript
{
  matches: [
    {
      message: "Use 'have gone' instead of 'have went'",
      shortMessage: "Grammar",
      replacements: [
        { value: "have gone" }
      ],
      offset: 2,
      length: 9,
      context: {
        text: "I have went to the store",
        offset: 2,
        length: 9
      },
      sentence: "I have went to the store yesterday.",
      rule: {
        id: "HAVE_VB",
        description: "Use of 'have' with past participle",
        category: {
          id: "GRAMMAR",
          name: "Grammar"
        }
      },
      word: "have went"
    }
  ]
}
```

## LanguageTool API

This library uses the LanguageTool API for grammar checking. You can:

1. **Use the free public API** (default) - No API key required, but has rate limits
2. **Use a premium API key** - Get higher rate limits and additional features
3. **Run your own LanguageTool server** - For complete control and privacy

### Getting an API Key

1. Visit [LanguageTool Premium](https://languagetool.org/premium/)
2. Sign up for an account
3. Get your API key
4. Use it in the `api_key` option

### Local LanguageTool Server

For better performance and privacy, you can run your own LanguageTool server:

```bash
# Using Docker
docker run -p 8010:8010 silviof/docker-languagetool

# Then use it in your code
const options = {
  api_url: 'http://localhost:8010/v2/check'
};
```

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

ISC License - see LICENSE file for details.

## Related Projects

- [gramma](https://github.com/caderek/gramma) - The original TypeScript version
- [LanguageTool](https://languagetool.org/) - The underlying grammar checking engine
