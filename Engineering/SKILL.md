# iOS Expo App Setup Skill

This skill provides a comprehensive guide for setting up a production-ready iOS Expo application, ensuring App Store compliance and deep integration with native iOS features.

## 1. App Store Compliance Checklist

To avoid rejection, every iOS app must include certain legal and account management features.

### A. Privacy Policy & Terms of Service
Apple requires these to be easily accessible. A common pattern is to host them as simple text in a Modal or navigate to a dedicated screen.

**Implementation (Modal Pattern):**
```tsx
const [modalContent, setModalContent] = useState<{ visible: boolean; title: string; content: string } | null>(null);

const openLegalModal = (type: 'terms' | 'privacy') => {
    setModalContent({
        visible: true,
        title: type === 'terms' ? 'Terms of Service' : 'Privacy Policy',
        content: type === 'terms' ? TERMS_TEXT : PRIVACY_TEXT
    });
};

// In render:
<Modal
    visible={!!modalContent}
    presentationStyle="pageSheet"
    animationType="slide"
>
    <View style={styles.header}>
        <Text>{modalContent?.title}</Text>
        <Button title="Close" onPress={() => setModalContent(null)} />
    </View>
    <ScrollView padding={20}>
        <Text>{modalContent?.content}</Text>
    </ScrollView>
</Modal>
```

### B. Reset / Delete Account Data
Apple requires that if an app supports account creation, it must also support account deletion. For local-only apps, a "Reset Data" button is often required to clear personal info.

**Implementation (Data Clearing):**
```tsx
import { userDefaultsRemove } from 'react-native-device-activity'; // If using native defaults
import AsyncStorage from '@react-native-async-storage/async-storage';

export const clearAllData = async () => {
    try {
        await AsyncStorage.clear();
        // Clear custom native keys if applicable
        userDefaultsRemove('PERSISTENCE_KEY');
        // Reset app state
        router.replace('/onboarding');
    } catch (e) {
        console.error('Failed to clear data', e);
    }
};
```

---

## 2. iOS Deep Integrations (Native Modules)

High-quality apps often integrate deeply with iOS APIs. This requires specific `app.json` configurations.

### A. Screen Time & Family Controls
Used in apps like `Well-Earned` to block apps or monitor usage.
- **Entitlements**: `com.apple.developer.family-controls` must be set to `true`.
- **App Groups**: Required for communication between the app and its extensions (e.g., `group.com.yourboundle.app`).

### B. Info.plist Reasoning Strings
You MUST provide clear justifications for why you use certain APIs. These are configured in `app.json` under `ios.infoPlist`.

```json
"ios": {
  "infoPlist": {
    "NSCameraUsageDescription": "Used to verify habit completion.",
    "NSUserTrackingUsageDescription": "Used to provide personalized analytics.",
    "ITSAppUsesNonExemptEncryption": false
  }
}
```

---

## 3. EAS Build & Secret Management

Never check API keys (RevenueCat, PostHog, OpenAI) directly into your repository.

### A. Secure Secrets
1. Use `expo-constants` or `.env` files for local dev.
2. Use **EAS Secrets** for production:
   ```bash
   eas secret:create --name REVENUECAT_API_KEY --value your_key_here
   ```
3. Access in `app.config.ts`:
   ```ts
   export default {
     extra: {
       revenueCatKey: process.env.REVENUECAT_API_KEY
     }
   }
   ```

### B. Backend Proxy (Pro Tip)
For high-security keys (like OpenAI), create a simple backend route (using Supabase Edge Functions or a small Node server) to proxy requests, so the key never touches the client.

---

## 4. Tech Stack Recommendations

| Feature | Recommended Tool | Rationale |
| :--- | :--- | :--- |
| **Analytics** | [PostHog](https://posthog.com) | Best-in-class event tracking and session replays. |
| **Monetization** | [RevenueCat](https://www.revenuecat.com) | Standard for managing Paywalls and cross-platform subscriptions. |
| **Styling** | [NativeWind](https://www.nativewind.dev) | Use Tailwind CSS patterns for rapid, responsive UI development. |
| **Navigation** | [Expo Router](https://docs.expo.dev/router/introduction/) | File-based routing that feels like Next.js for mobile. |

---

## 5. Layout Boilerplates

### Option 1: Expo Router (The Modern Way)
Structure your `app/` directory for automatic routing.
- `app/_layout.tsx`: Root provider (Theme, PostHog, RevenueCat).
- `app/(tabs)/_layout.tsx`: Tab bar configuration.
- `app/(auth)/login.tsx`: Protected entry point.

### Option 2: Traditional Pager (High-Control)
Use a state-driven `ScrollView` with `pagingEnabled` for custom onboarding or dashboard transitions. This allows for perfectly synchronized animations between screens.

---

## 6. Development Workflow Tips
- **EAS Update**: Use this for hot-fixing bugs without a full App Store review.
- **Development Builds**: Always use `eas build --profile development` instead of the standard Expo Go if you have native modules like `react-native-device-activity`.
