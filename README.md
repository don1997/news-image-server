app.get('/', (req, res) => {
  const userAgent = req.get('user-agent');

  const options = {
    host: 'newsapi.org',
    path: `/v2/top-headlines?country=us&apiKey=${apiKey}`,
    headers: {
      'User-Agent': userAgent
    },
    method: 'GET'
  };

  const request = http.request(options, (response) => {
    let data = '';

    response.on('data', (chunk) => {
      data += chunk;
    });

    response.on('end', () => {
      const newsData = JSON.parse(data);
      const articles = newsData.articles;

      articles.forEach((article) => {
        if (article.source.name === 'Google News') {
          // Extract the image URL from the article's description or content
          parser.parseString(article.description || article.content, (err, result) => {
            if (err) {
              console.error(`Error parsing XML data: ${err.message}`);
              return;
            }
            article.imageUrl = result.item.media:content.url;
          });
        }
      });

      res.json(newsData);
    });
  });

  request.on('error', (error) => {
    console.error(`Error making request: ${error.message}`);
  });

  request.end();
});
