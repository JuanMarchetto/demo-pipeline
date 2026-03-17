# Demo Pipeline — State Injection Reference

How demo state injection works across platforms.

## Expo / React Native (Mobile)

### App-side bootstrap

Create `src/demo/bootstrap.ts` in the target project (dev-only, zero production impact):

```typescript
import AsyncStorage from '@react-native-async-storage/async-storage';

const DEMO_STATE_KEY = '__DEMO_STATE__';

export async function loadDemoState(): Promise<Record<string, any> | null> {
  if (!__DEV__) return null;

  try {
    const raw = await AsyncStorage.getItem(DEMO_STATE_KEY);
    if (!raw) return null;
    return JSON.parse(raw);
  } catch {
    return null;
  }
}

export async function clearDemoState(): Promise<void> {
  await AsyncStorage.removeItem(DEMO_STATE_KEY);
}
```

### Import in app entry

In `src/app/_layout.tsx` or equivalent, add at the top:

```typescript
import { loadDemoState } from '../demo/bootstrap';

// In the root component useEffect:
useEffect(() => {
  if (__DEV__) {
    loadDemoState().then(state => {
      if (state) {
        // Apply mock state to your context/providers
        console.log('[DEMO] Loaded mock state:', state);
      }
    });
  }
}, []);
```

### Injection via adb (done by the skill)

```bash
# Inject state before running demo
adb shell "run-as <APP_ID> sh -c \"
  mkdir -p /data/data/<APP_ID>/files/
  echo '{\"wallet\":\"0x123\",\"balance\":5000}' > /data/data/<APP_ID>/files/demo-state.json
\""

# Alternative: use AsyncStorage directly (RN stores in SQLite)
adb shell "run-as <APP_ID> sqlite3 /data/data/<APP_ID>/databases/RKStorage" \
  "INSERT OR REPLACE INTO catalystLocalStorage VALUES ('__DEMO_STATE__', '{\"wallet\":\"0x123\"}');"
```

### Injection via deep links

Register a demo-only deep link handler:

```typescript
// In _layout.tsx or navigation config
if (__DEV__) {
  Linking.addEventListener('url', ({ url }) => {
    const parsed = new URL(url);
    if (parsed.pathname === '/demo') {
      const state = JSON.parse(decodeURIComponent(parsed.searchParams.get('state') || '{}'));
      // Apply state to providers
    }
  });
}
```

Skill triggers via:
```bash
adb shell am start -a android.intent.action.VIEW \
  -d "myapp://demo?state=%7B%22balance%22%3A5000%7D"
```

### Native mock pattern

For native modules (Camera, Seed Vault, etc.), the bootstrap can override:

```typescript
if (__DEV__ && demoState?.scan_result) {
  // Skip camera, return mock result directly
  global.__DEMO_SCAN_RESULT__ = demoState.scan_result;
}
```

In the scan screen, check before opening camera:
```typescript
if (__DEV__ && global.__DEMO_SCAN_RESULT__) {
  handleScanResult(global.__DEMO_SCAN_RESULT__);
  return;
}
// ... normal camera flow
```

### Cleanup

```bash
adb shell "run-as <APP_ID> sqlite3 /data/data/<APP_ID>/databases/RKStorage" \
  "DELETE FROM catalystLocalStorage WHERE key = '__DEMO_STATE__';"
```

## Web (Next.js / Generic)

### Injection via localStorage

```bash
# webreel/Playwright can inject via JavaScript evaluation
page.evaluate(() => {
  localStorage.setItem('__DEMO_STATE__', JSON.stringify({
    wallet: '0x123',
    balance: 5000
  }));
  location.reload();
});
```

### Cleanup

```javascript
localStorage.removeItem('__DEMO_STATE__');
```
