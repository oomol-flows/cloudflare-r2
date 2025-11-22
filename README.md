# Cloudflare R2 Upload

A reliable and efficient file upload solution for OOMOL workflows that enables seamless file transfer to cloud storage with automatic presigned URL generation.

## Overview

This OOMOL package provides a robust file upload block that handles chunked file uploads to cloud storage. It automatically manages large file transfers through multipart uploading, provides real-time progress tracking, and generates time-limited presigned URLs for secure file access.

## Features

- **Chunked Upload Support**: Automatically splits large files into manageable chunks for reliable uploading
- **Progress Tracking**: Real-time upload progress reporting integrated with OOMOL's progress system
- **Automatic Retry**: Built-in retry mechanism for failed chunk uploads (up to 3 attempts per chunk)
- **Parallel Processing**: Uploads multiple chunks simultaneously for optimal performance
- **Presigned URL Generation**: Automatically generates secure, time-limited access URLs (valid for 24 hours)
- **Error Handling**: Comprehensive error handling with detailed error messages

## Blocks

### Upload File to Cloud

**Block Name**: `upload-to-cloudflare`

Uploads a local file to cloud storage and returns a presigned URL for accessing the file.

#### Inputs

- **file** (string, required): Local file path to upload to cloud storage
  - Supports file picker widget for easy file selection
  - Accepts any file type and size

#### Outputs

- **remote_url** (string): Presigned URL for accessing the uploaded file
  - Valid for 24 hours from generation
  - Can be used directly for downloading or sharing the file
  - Format: HTTPS URL pointing to the cloud storage location

#### How It Works

1. **Initialization**: The block analyzes the file size and requests an upload session from the cloud service
2. **Chunking**: Large files are automatically divided into optimized chunks (part size determined by the server)
3. **Upload**: Chunks are uploaded in parallel with automatic retry on failure
4. **Completion**: After all chunks are successfully uploaded, a presigned URL is generated
5. **Progress**: Upload progress is continuously reported (0-100%)

## Usage Example

### In a Workflow

1. Add the "Upload File to Cloud" block to your workflow
2. Connect a file path (either from a file picker or another block's output)
3. The block will upload the file and output a presigned URL
4. Use the presigned URL in subsequent blocks (e.g., sharing, processing, or displaying)

### Common Use Cases

- **File Sharing**: Upload files and share the presigned URL with others
- **Data Processing Pipelines**: Upload input files before processing them with other services
- **Backup Solutions**: Automatically backup files to cloud storage
- **Media Hosting**: Upload images, videos, or audio files for web applications
- **Document Management**: Store and retrieve documents through presigned URLs

## Technical Details

### Architecture

The upload process uses a three-phase architecture:

1. **Initialization Phase**:
   - Sends file metadata (extension and size) to the server
   - Receives upload configuration (upload_id, part_size, total_parts, presigned_urls)

2. **Upload Phase**:
   - Splits file into chunks based on server-provided part_size
   - Uploads chunks in parallel using presigned URLs
   - Implements exponential backoff retry strategy (1s, 2s, 3s delays)
   - Reports progress after each successful chunk upload

3. **Finalization Phase**:
   - Requests the final presigned URL using the upload_id
   - Returns the URL valid for 24 hours

### API Endpoints

- **Initialize Upload**: `POST https://llm.oomol.com/api/tasks/files/remote-cache/init`
- **Get Final URL**: `GET https://llm.oomol.com/api/tasks/files/remote-cache/{upload_id}/url`
- **Chunk Upload**: Direct PUT requests to presigned URLs

### Authentication

The block automatically uses the OOMOL token for authentication. No manual API key configuration is required.

### Performance Optimization

- Parallel chunk uploads maximize bandwidth utilization
- Automatic chunk sizing based on file size
- Retry mechanism prevents failures from network issues
- Progress reporting provides user feedback

### Error Handling

The block handles various error scenarios:

- File not found errors
- Network connectivity issues
- Server errors (with descriptive status codes)
- Missing presigned URLs
- Upload timeout failures

All errors include detailed messages for troubleshooting.

## Installation

This package is available in the OOMOL package registry. Install it using:

```bash
# Search for the package
oomol search cloudflare-r2

# Install the package
oomol install cloudflare-r2

# Add to workspace dependencies
oomol use cloudflare-r2
```

## Requirements

- Node.js runtime (configured automatically by OOMOL)
- OOMOL platform with token authentication
- Internet connectivity for cloud storage access

## Dependencies

All dependencies are managed automatically through npm:

- `@oomol/types`: OOMOL type definitions and context APIs
- Built-in Node.js modules: `fs`, `path`

## Configuration

No manual configuration is required. The block automatically:

- Retrieves authentication tokens from OOMOL context
- Determines optimal chunk sizes from the server
- Handles all upload parameters automatically

## Limitations

- Presigned URLs expire after 24 hours
- Upload speed depends on network bandwidth
- Large files may take time to upload (progress is reported)

## Contributing

Contributions are welcome! Please visit the [GitHub repository](https://github.com/oomol-flows/cloudflare-r2) to:

- Report issues
- Submit pull requests
- Request features
- View source code

## License

Please refer to the repository for license information.

## Support

For support and questions:

- Open an issue on [GitHub](https://github.com/oomol-flows/cloudflare-r2/issues)
- Check the OOMOL documentation
- Contact the OOMOL community

---

**Version**: 0.0.1
**Repository**: https://github.com/oomol-flows/cloudflare-r2
