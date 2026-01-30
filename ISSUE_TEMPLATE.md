# Feature Request: Enhanced HTTP Password Field Extraction

## Summary
This feature request proposes several enhancements to improve HTTP password field extraction capabilities in PCredz, including HTTPS support, HTTP response scanning, better field name matching, and improved display formatting.

## Proposed Features

### 1. HTTPS Support (`--enable-https`)
- **Current behavior**: Skips packets on ports 443 and 8443 (HTTPS ports)
- **Proposed change**: Add `--enable-https` flag to enable processing of HTTPS traffic
- **Use case**: When SSL/TLS decryption is available or for testing purposes

### 2. HTTP Response Scanning (`--enable-http-response`)
- **Current behavior**: Only processes HTTP requests
- **Proposed change**: Add `--enable-http-response` flag to optionally scan HTTP responses
- **Use case**: Some applications may leak credentials in response bodies or headers

### 3. Enhanced Host Header Extraction
- **Current behavior**: No Host information displayed
- **Proposed change**: Extract and display Host header from HTTP requests
- **Benefits**: Better context for identifying which service the credentials belong to

### 4. Improved Field Name Matching
- **Current behavior**: Only matches exact field names like `password`, `token`, etc.
- **Proposed change**: Match field names containing password keywords (e.g., `luci_password`, `user_password`, `admin_token`)
- **Benefits**: Catches more credential fields with prefixed/suffixed names

### 5. Better POST Body Support
- **Current behavior**: May miss password fields in POST request bodies
- **Proposed change**: 
  - Add `re.MULTILINE` flag to regex for better POST body matching
  - Improved line extraction logic for form-encoded data
- **Benefits**: Reliably captures credentials submitted via POST forms

### 6. Cleaner Output Format
- **Current behavior**: May display entire HTTP request packets
- **Proposed change**: Only display the specific line containing the matched password field
- **Benefits**: Cleaner, more focused output that's easier to read

## Implementation Details

### New Command-line Arguments
```bash
--enable-https          # Enable HTTPS decryption (ports 443, 8443)
--enable-http-response  # Enable HTTP response scanning
```

### Code Changes
1. Updated `extract_http_password_fields()` function signature to accept `enable_https` and `enable_http_response` parameters
2. Enhanced regex pattern to match field names containing password keywords
3. Added Host header extraction logic
4. Improved line extraction and display logic to show only matched lines
5. Added HTTP request/response detection

### Example Output
**Before:**
```
192.168.1.1:80 > 192.168.1.2:12345
Potential password submission:
Request: [entire HTTP request packet]
```

**After:**
```
192.168.1.1:80 > 192.168.1.2:12345
Host: example.com
Potential password submission:
Request: luci_username=root&luci_password=admin123
```

## Testing
- Tested with various HTTP requests (GET, POST)
- Tested with URL query parameters
- Tested with POST form data
- Tested with HTTP headers (Cookie, Authorization)
- Verified Host extraction works correctly
- Confirmed only matched lines are displayed

## Backward Compatibility
- All changes are backward compatible
- New flags default to `False` (disabled)
- Existing functionality remains unchanged when flags are not used

## Benefits
1. **More comprehensive credential capture**: Catches credentials in more scenarios (POST bodies, prefixed field names)
2. **Better context**: Host information helps identify which service the credentials belong to
3. **Cleaner output**: Easier to read and analyze results by showing only relevant lines
4. **Flexible scanning**: Optional HTTPS and response scanning for different use cases
5. **Backward compatible**: All changes are optional and don't affect existing functionality

## Implementation Status
✅ Code implemented and tested
- All features are working as described
- Tested with various HTTP requests and responses
- Backward compatible with existing code

## Files Modified
- `Pcredz` (main script)
  - Added `--enable-https` and `--enable-http-response` command-line arguments
  - Enhanced `extract_http_password_fields()` function with new parameters
  - Updated regex pattern with `re.MULTILINE` flag and improved field name matching
  - Added Host header extraction logic
  - Improved line extraction and display logic

## Request
I would be happy to submit a pull request with these changes if you're interested in merging them. The implementation is complete, tested, and ready for review.
