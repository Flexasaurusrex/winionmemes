// Vercel Serverless Function
// This keeps your API key secure on the server

// Helper function to retry requests
async function fetchWithRetry(url, options, maxRetries = 2) {
  for (let i = 0; i <= maxRetries; i++) {
    try {
      const response = await fetch(url, options);
      
      // Don't retry on client errors (4xx), only server errors (5xx) or rate limits
      if (response.ok || (response.status >= 400 && response.status < 500 && response.status !== 429)) {
        return response;
      }
      
      // If rate limited or server error, wait and retry
      if (i < maxRetries) {
        const waitTime = Math.pow(2, i) * 1000; // Exponential backoff: 1s, 2s
        console.log(`Retry ${i + 1}/${maxRetries} after ${waitTime}ms`);
        await new Promise(resolve => setTimeout(resolve, waitTime));
      } else {
        return response;
      }
    } catch (error) {
      if (i === maxRetries) throw error;
      await new Promise(resolve => setTimeout(resolve, 1000));
    }
  }
}

export default async function handler(req, res) {
  // Add CORS headers
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'POST, OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
  
  // Handle OPTIONS preflight
  if (req.method === 'OPTIONS') {
    return res.status(200).end();
  }
  
  // Only allow POST requests
  if (req.method !== 'POST') {
    return res.status(405).json({ error: 'Method not allowed' });
  }

  try {
    const { prompt } = req.body;

    if (!prompt) {
      return res.status(400).json({ error: 'Prompt is required' });
    }

    // Check if API key exists
    if (!process.env.TOGETHER_API_KEY) {
      console.error('TOGETHER_API_KEY environment variable is not set');
      return res.status(500).json({ 
        error: 'Server configuration error: TOGETHER_API_KEY not configured in Vercel environment variables' 
      });
    }

    console.log('Generating image with Together.ai...');

    // Strong negative prompt to avoid clean renders
    const negativePrompt = 'clean, smooth, high definition, HD, 4K, sharp, clear, modern, 3D render, CGI, polished, professional photography, studio lighting, gradient, soft focus, bokeh, realistic, photorealistic, detailed, high quality, pristine, perfect, immaculate';

    // Call Together.ai API with retry logic
    const response = await fetchWithRetry('https://api.together.xyz/v1/images/generations', {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.TOGETHER_API_KEY}`,
        'Content-Type': 'application/json',
      },
      body: JSON.stringify({
        model: 'black-forest-labs/FLUX.1-schnell', // Removed '-Free' to use paid tier
        prompt: prompt,
        negative_prompt: negativePrompt,
        width: 512,
        height: 512,
        steps: 8, // Increased for better prompt adherence
        n: 1
      })
    });

    // Handle rate limiting
    if (response.status === 429) {
      console.log('Rate limit hit (429)');
      return res.status(429).json({ 
        error: 'Rate limit reached. This shouldn\'t happen with Enterprise. Please check your Together.ai dashboard.' 
      });
    }

    // Handle authentication errors
    if (response.status === 401) {
      console.error('Authentication failed (401)');
      return res.status(401).json({ 
        error: 'API authentication failed. Please verify your API key in Vercel settings.' 
      });
    }

    if (!response.ok) {
      const errorData = await response.json();
      console.error('Together.ai error:', response.status, errorData);
      
      return res.status(response.status).json({ 
        error: errorData.error?.message || 'Failed to generate image',
        details: errorData,
        status: response.status
      });
    }

    const data = await response.json();
    console.log('Image generated successfully');
    return res.status(200).json(data);

  } catch (error) {
    console.error('Server error:', error);
    console.error('Error stack:', error.stack);
    return res.status(500).json({ 
      error: 'Internal server error',
      message: error.message,
      details: process.env.NODE_ENV === 'development' ? error.stack : undefined
    });
  }
}
