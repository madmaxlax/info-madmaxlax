# Migration from ipwho.is to ip-api.com

## Why the Change?
ipwho.is removed CORS support from their free tier, causing browser-based requests to fail with:
```json
{"success": false, "message": "CORS is not supported on the Free plan"}
```

## Solution: ip-api.com
- **Free tier**: 45 requests/minute (plenty for personal use)
- **CORS**: ✅ Fully supported on free tier
- **No API key required**
- **Running since 2012** - very stable

## Code Changes

### 1. API URL Change
```javascript
// OLD
var url = "//ipwho.is/";

// NEW
var url = "//ip-api.com/json/";
```

### 2. Field Name Mappings

| Data | ipwho.is | ip-api.com |
|------|----------|------------|
| IP Address | `ip` | `query` |
| City | `city` | `city` |
| State/Region | `region` | `regionName` |
| Country Name | `country` | `country` |
| Country Code | `country_code` | `countryCode` |
| Timezone (name) | `timezone.id` | `timezone` |
| Timezone (time) | `timezone.current_time` | ❌ Not provided |
| ISP | `connection.isp` | `isp` |
| Organization | `connection.org` | `org` |

### 3. Updated Code Sections

**IP Address:**
```javascript
// OLD
ipAddress = locObj.ip;

// NEW
ipAddress = locObj.query;
```

**Region/State:**
```javascript
// OLD
state = locObj["region"];

// NEW
state = locObj["regionName"];
```

**Country Code:**
```javascript
// OLD
countryEmoji = locObj["country_code"].toUpperCase()...

// NEW
countryEmoji = locObj["countryCode"].toUpperCase()...
```

**Timezone:**
```javascript
// OLD
time = locObj["timezone"];
currentTime = new Date(time.current_time);

// NEW
time = locObj["timezone"];
currentTime = new Date(); // Use local time instead
```

**ISP/Org:**
```javascript
// OLD
ispOrg = locObj.connection.isp + 
    (locObj.connection.isp !== locObj.connection.org ? 
        "-" + locObj.connection.org : "");

// NEW
ispOrg = locObj.isp + 
    (locObj.isp !== locObj.org ? 
        "-" + locObj.org : "");
```

## Testing
1. Backup original: `cp index.html index-backup.html`
2. Replace with fixed version: `cp index-fixed.html index.html`
3. Test both HTTP and HTTPS access
4. Verify VPN detection still works
5. Check timezone display

## Rollback Plan
If issues occur:
```bash
cp index-backup.html index.html
```

## Alternative APIs (if needed)
1. **ipapi.co** - 30k/month free, HTTPS included
2. **freeipapi.com** - 60 req/min free, commercial allowed
3. **ipwho.is paid** - $4/month for CORS support
