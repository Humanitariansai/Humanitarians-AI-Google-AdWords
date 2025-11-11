# Fixing Google Ad Grants Zero Impressions: Complete Implementation Guide

## Problem Summary
Your Google Ad Grants account is experiencing zero impressions despite high relevancy scores. This indicates a technical compliance issue - Google requires conversion data to enable Smart Bidding, which is necessary for Ad Grants campaigns to show.

## Solution Overview
1. Set up all three campaigns (Lyrical Literacy, Botspeak, Humanitarians AI)
2. Add Google Ads conversion tracking to your React application
3. Implement micro-conversion tracking to give Google's algorithm the data it needs

## Required Actions

### 1. Campaign Setup (Google Ads Dashboard)
- Create all 3 campaigns with at least 2 ad groups each
- Set bidding strategy to "Maximize Conversions" 
- Ensure keywords have Quality Score ≥3
- Add responsive search ads with multiple headlines/descriptions
- Set up conversion actions in Google Ads dashboard

### 2. Technical Implementation (React Application)

#### Step 1: Add Global Tracking Code
Add to your `public/index.html` file in the `<head>` section:

```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=AW-CONVERSION_ID"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'AW-CONVERSION_ID');
</script>
```

#### Step 2: Create Tracking Hook
```jsx
// src/hooks/useTracking.js
export const useTracking = () => {
  const trackEvent = (action, category, label, value = null) => {
    if (window.gtag) {
      window.gtag('event', action, {
        'event_category': category,
        'event_label': label,
        ...(value !== null && { 'value': value })
      });
    }
  };

  return { trackEvent };
};
```

#### Step 3: Add Micro-Conversion Tracking to Components

**Button Clicks:**
```jsx
import { useTracking } from '../hooks/useTracking';

function DownloadButton() {
  const { trackEvent } = useTracking();
  
  const handleClick = () => {
    trackEvent('download', 'botspeak', 'ai_literacy_guide');
    // Download logic
  };

  return <button onClick={handleClick}>Download Guide</button>;
}
```

**Form Submissions:**
```jsx
function ApplicationForm() {
  const { trackEvent } = useTracking();
  
  const handleSubmit = (e) => {
    e.preventDefault();
    trackEvent('submit', 'fellows_program', 'application_form');
    // Form submission logic
  };

  return <form onSubmit={handleSubmit}>...</form>;
}
```

**Scroll Depth:**
```jsx
import { useEffect, useRef } from 'react';
import { useTracking } from '../hooks/useTracking';

function ContentPage() {
  const { trackEvent } = useTracking();
  const scrollTracked = useRef(false);
  
  useEffect(() => {
    const handleScroll = () => {
      if (scrollTracked.current) return;
      
      const scrollPercent = 100 * window.scrollY / (document.body.offsetHeight - window.innerHeight);
      if (scrollPercent > 75) {
        trackEvent('scroll', 'engagement', '75_percent');
        scrollTracked.current = true;
      }
    };
    
    window.addEventListener('scroll', handleScroll);
    return () => window.removeEventListener('scroll', handleScroll);
  }, [trackEvent]);

  return <div>Page content...</div>;
}
```

**Video Views:**
```jsx
function VideoPlayer() {
  const { trackEvent } = useTracking();
  
  const handleVideoStart = () => {
    trackEvent('video', 'lyrical_literacy', 'start_tutorial');
  };
  
  // Add similar handlers for 25%, 50%, 75%, complete
  
  return <video onPlay={handleVideoStart}>...</video>;
}
```

### Key Micro-Conversions to Track:
- Page scroll depth (75%)
- Video engagement (start, complete)
- Button clicks (learn more, download)
- Form interactions (start, submit)
- Page time spent (30+ seconds)

## Timeline & Expected Results
1. Implement tracking: 1-2 days
2. Data collection period: 1-2 weeks
3. Expected outcome: Google's algorithm learns which users find value in your content, enabling Smart Bidding to function properly
4. Result: Your ads should start getting impressions

Remember: The connection between campaigns and conversions happens automatically through the user's click path. Google tracks which campaign drove a user to your site and attributes any conversions they complete back to that campaign.

