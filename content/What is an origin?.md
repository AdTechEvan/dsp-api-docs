# What is a HTTP origin

An **HTTP origin** uniquely identifies where a request is coming from, and is a combination of a URL's:
- **scheme** (e.g., `http` or `https`)
- **host** (domain or IP address)
- **port** 
	- The port can generally be omitted, and will behave as if it is equal to `80` when the scheme is `http`, or equal to `443` when the scheme is `https`.

## Examples

| Example                      | Valid?    |
| ---------------------------- | --------- |
| `http://example.com:80`      | Valid     |
| `http://example.com:80/path` | Not valid |
| `https://example.com`        | Valid     |

## More Info

 - [URL: origin property - Web APIs \| MDN](https://developer.mozilla.org/en-US/docs/Web/API/URL/origin)
