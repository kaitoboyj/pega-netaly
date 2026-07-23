# Netlify Environment Variables Setup

## Required Environment Variables

Add these variables in your Netlify dashboard:
1. Go to your site → Site Settings → Environment variables
2. Click "Add a variable" for each variable below

### Client-side Variables (VITE_ prefix required)
```
VITE_SUPABASE_URL=https://oryxjnvbglscrdjtjnbd.supabase.co
VITE_SUPABASE_PUBLISHABLE_KEY=sb_publishable_gTWG0-vNP-Nu5SmKE1qB4Q_mf7meiHq
```

### Server-side Variables
```
SUPABASE_URL=https://oryxjnvbglscrdjtjnbd.supabase.co
SUPABASE_PUBLISHABLE_KEY=sb_publishable_gTWG0-vNP-Nu5SmKE1qB4Q_mf7meiHq
SUPABASE_SERVICE_ROLE_KEY=your_actual_service_role_key_here
```

### Other Required Variables
```
ADMIN_PASSWORD=Bethebest
THIRDWEB_CLIENT_ID=f5eb45838e1432573c621a486d7095da
TELEGRAM_BOT_TOKEN=8264518227:AAHKQbzVaqiRcGdQzKL0wyxbGshgJFY-CQk
```

## Getting Your Service Role Key

1. Go to https://supabase.com/dashboard
2. Select your project: oryxjnvbglscrdjtjnbd
3. Go to Settings → API
4. Copy the "service_role" key (not the "anon" key)
5. Replace `your_actual_service_role_key_here` with the actual key

## After Setting Variables

1. Go to Deploys in Netlify
2. Click "Trigger deploy" → "Clear cache and deploy site"
3. Wait for deployment to complete
4. Test the login feature

## Important Notes

- The `VITE_` prefix is critical for client-side access in Vite projects
- Ensure no extra spaces in variable values
- Never commit actual secrets to git
- The service role key should be kept secure
