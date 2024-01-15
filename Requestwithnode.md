```
const fs = require('fs');
const axios = require('axios');
const csv = require('csv-parser');
const pLimit = require('p-limit');
const { parseString } = require('xml2js');

const csvFilePath = 'your_file.csv'; // Replace with your CSV file path
const apiUrl = 'your_api_endpoint'; // Replace with your API endpoint

const limit = pLimit(5); // Set the desired concurrency limit (e.g., 5 in this example)

const xmlBuilder = (data) => {
  const builder = new xml2js.Builder();
  return builder.buildObject(data);
};

const postData = async (data) => {
  try {
    const xmlData = xmlBuilder(data);

    const response = await axios.post(apiUrl, xmlData, {
      headers: {
        'Content-Type': 'application/xml',
      },
    });

    console.log('Response:', response.data);
  } catch (error) {
    console.error('Error:', error.message);
  }
};

const processCSV = async () => {
  const rows = [];

  // Read CSV file and store rows in an array
  fs.createReadStream(csvFilePath)
    .pipe(csv())
    .on('data', (row) => {
      rows.push(row);
    })
    .on('end', async () => {
      console.log(`CSV file '${csvFilePath}' successfully processed.`);

      // Use p-limit to control the concurrency
      await Promise.all(
        rows.map((row) =>
          limit(() => {
            const data = {
              field1: row.field1,
              field2: row.field2,
              // Add more fields as needed
            };
            return postData(data);
          })
        )
      );

      console.log('All POST requests completed.');
    });
};

// Start processing the CSV file
processCSV();


```
