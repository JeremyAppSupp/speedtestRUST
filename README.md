# speedtestRUST# Rusty SpeedTest

A robust, efficient network speed testing tool written in Rust for measuring download speeds, upload speeds, and ping times.

## Features

- **Comprehensive Testing**: Measures download speed, upload speed, and ping latency
- **Jitter Analysis**: Calculates network jitter for all measurements
- **Multiple Output Formats**: Supports simple text, JSON, and CSV output formats
- **Error Handling**: Robust error handling with detailed error reporting
- **Retry Logic**: Automatic retry with exponential backoff for failed requests
- **Memory Efficient**: Streaming uploads to avoid excessive memory usage
- **Configurable Tests**: Skip any test type (ping, download, upload) as needed
- **Concurrent or Sequential**: Run tests concurrently or sequentially for different scenarios
- **Timeouts**: Configurable request timeouts to prevent hanging tests

## Installation

### Prerequisites

- [Rust and Cargo](https://www.rust-lang.org/tools/install) (1.60 or newer)

### Building from Source

```bash
# Clone the repository
git clone https://github.com/yourusername/rusty-speedtest.git
cd rusty-speedtest

# Build the project in release mode
cargo build --release

# The binary will be available at ./target/release/rusty-speedtest
```

### Cargo Install

```bash
cargo install --git https://github.com/yourusername/rusty-speedtest.git
```

## Usage

```bash
# Basic usage with default settings
rusty-speedtest

# Specify a custom server
rusty-speedtest --server https://example.com/speedtest

# Use a separate upload server
rusty-speedtest --server https://download.example.com --upload-server https://upload.example.com

# Adjust test sizes
rusty-speedtest --download-size 20 --upload-size 10

# Skip specific tests
rusty-speedtest --skip-ping --skip-upload

# Output in JSON format
rusty-speedtest --format json

# Run tests sequentially
rusty-speedtest --sequential
```

### Command Line Options

| Option | Default | Description |
|--------|---------|-------------|
| `-s, --server` | `https://speed.cloudflare.com/__down` | Server URL to test against |
| `-u, --upload-server` | Same as server | Optional separate upload server URL |
| `-d, --download-size` | `10` | Size of download test in MB |
| `-u, --upload-size` | `5` | Size of upload test in MB |
| `--skip-ping` | `false` | Skip ping test |
| `--skip-download` | `false` | Skip download test |
| `--skip-upload` | `false` | Skip upload test |
| `-f, --format` | `simple` | Output format (simple, json, csv) |
| `--sequential` | `false` | Run tests sequentially instead of concurrently |

## Output Formats

### Simple Text (Default)

```
Ping: 25.43 ms (Jitter: 1.87 ms)
Download: 95.67 Mbps (Jitter: 3.21 Mbps)
Upload: 42.19 Mbps (Jitter: 2.56 Mbps)
```

### JSON

```json
{
  "ping": {
    "value": 25.43,
    "jitter": 1.87,
    "error": null
  },
  "download": {
    "value": 95.67,
    "jitter": 3.21,
    "error": null
  },
  "upload": {
    "value": 42.19,
    "jitter": 2.56,
    "error": null
  }
}
```

### CSV

```csv
test,value,jitter,error
ping,25.43,1.87,
download,95.67,3.21,
upload,42.19,2.56,
```

## Environment Variables

- `RUST_LOG`: Controls logging level (e.g., `info`, `debug`, `error`)

## Technical Details

### How Tests Are Performed

#### Ping Test
- Sends HTTP HEAD requests to the specified server
- Measures round-trip time for each request
- Performs 5 measurements and calculates average and jitter

#### Download Test
- Downloads chunks of data from the specified server
- Measures download speed based on time and amount of data
- Performs 5 measurements and calculates average and jitter

#### Upload Test
- Uploads random data to the specified server in chunks
- Measures upload speed based on time and amount of data
- Performs 5 measurements and calculates average and jitter
- Falls back to simulation if the server doesn't accept uploads

### Handling Network Issues

- Connection failures trigger retries with exponential backoff
- Timeouts prevent tests from hanging indefinitely
- All errors are captured and reported in the output

## Performance Considerations

- By default, tests run concurrently for faster overall execution
- Use `--sequential` for more accurate results on limited connections
- Jitter values help identify unstable connections
- The tool uses streaming for uploads to minimize memory usage

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## License

This project is licensed under the MIT License - see the LICENSE file for details.

## Acknowledgments

- Speed test servers provided by various public services
- Built with Rust and the following crates:
  - tokio for async runtime
  - reqwest for HTTP requests
  - clap for command line parsing
  - log and env_logger for logging
  - serde_json for JSON formatting