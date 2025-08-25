---
created: 2025-02-24T17:10
updated: 2025-02-25T22:15
cssclasses:
  - wide-page
sticker: lucide//bar-chart-4
banner: Archive/Assets/03.png
---

--- start-multi-column: Dashboard
```column-settings 
number of columns: 3
Border: disabled  
Shadow: off
```

```dataviewjs
const notes = dv.pages().filter(p => p.file.path.includes("3_Explore Future Possibilities/")); 

dv.paragraph(`
<div style="border: solid 1px lightgrey; padding: 10px 20px; border-radius: 10px">
	<h1 style="font-size:3rem">${notes.length}</h1>
	<p>Total Notes</p>

</div>`)
```

--- end-column ---

```dataviewjs
const pagesInFolder = dv.pages().filter(p => p.file.path.includes("3_Explore Future Possibilities/")).array();
const totalLinks = pagesInFolder.reduce((sum, page) => sum + (page.file.outlinks && page.file.outlinks.length ? page.file.outlinks.length : 0), 0);

dv.paragraph(`
<div style="border: solid 1px lightgrey; padding: 10px 20px; border-radius: 10px">
	<h1 style="font-size:3rem">${totalLinks}</h1>
	<p>Total Outgoing Links</p>

</div>`)

```

--- end-column ---

```dataviewjs
// Get folder notes
const notes = dv.pages().filter(p => p.file.path.includes("3_Explore Future Possibilities/"));

// Count total size
let totalSize = 0;
notes.forEach(note => {
  if (note.file.size) {
    totalSize += note.file.size;
  }
});

// Estimate words (rough approximation)
const estimatedWords = Math.round(totalSize / 5.5);


dv.paragraph(`
<div style="border: solid 1px lightgrey; padding: 10px 20px; border-radius: 10px">
	<h1 style="font-size:3rem">${estimatedWords.toLocaleString()}</h1>
	<p>Estimated Total Words</p>

</div>`)

```

--- end-multi-column





--- start-multi-column: Dashboard2
```column-settings  
Border: disabled  
Shadow: off
```

# Notes distribution per Gate
```dataviewjs
const folderPath = "Feeding Healthy Futures/3_Explore Future Possibilities";
const files = dv.pages(`"${folderPath}"`);

// Object to store subfolder counts
const folderCounts = {};

files.forEach(file => {
    const relativePath = file.file.folder.replace(folderPath + "/", ""); // Get path relative to main folder
    const subfolder = relativePath.split("/")[0]; // Extract first-level subfolder

    // Only count notes in subfolders (ignore root-level notes)
    if (subfolder && relativePath !== file.file.folder) {
        folderCounts[subfolder] = (folderCounts[subfolder] || 0) + 1;
    }
});

const labels = Object.keys(folderCounts).map(label => `"${label}"`).join(", ");
const data = Object.values(folderCounts).join(", ");

dv.paragraph(`\`\`\`chart
    type: polarArea
    labels: [${labels}]
    series:
    - title: Notes Count
      data: [${data}]
    labelColors: true
\`\`\``);
```

--- end-column ---

# Tags Distribution
```dataviewjs
// Define folder path and tags to track
const folderPath = "Feeding Healthy Futures/3_Explore Future Possibilities";
const tagsToTrack = ["Past", "Present", "Future", "Transitions"];

// Get all files from the specified folder
const files = dv.pages(`"${folderPath}"`);

// Initialize counters for each tag
const tagCounts = {};
tagsToTrack.forEach(tag => tagCounts[tag] = 0);

// Count occurrences of each tag in the files
files.forEach(file => {
    if (file.file.tags) {
        file.file.tags.forEach(tag => {
            // Remove the # from the tag for comparison
            const cleanTag = tag.substring(1);
            if (tagsToTrack.includes(cleanTag)) {
                tagCounts[cleanTag] = (tagCounts[cleanTag] || 0) + 1;
            }
        });
    }
});

// Prepare the data for chart rendering in the template format
const labels = Object.keys(tagCounts).map(label => `"${label}"`).join(", ");
const data = Object.values(tagCounts).join(", ");

// Display the chart using the template syntax
dv.paragraph(`\`\`\`chart
    type: radar
    labels: [${labels}]
    series:
    - title: Tags
      data: [${data}]
    labelColors: true
\`\`\``);
```


--- end-multi-column







# Top 20 notes with the most links
```dataviewjs
// Define the folder path - make sure this matches your folder structure exactly
const folderPath = "Feeding Healthy Futures/3_Explore Future Possibilities";

// Get all files in the folder and subfolders
// Using a more compatible query format
const folderFiles = dv.pages().where(p => p.file.path.startsWith(folderPath));

// Create an array to store file data
let filesWithLinks = [];

// Process each file to count outgoing links
folderFiles.forEach(page => {
    try {
        // Get outgoing links (handle potential undefined values)
        const outlinks = page.file.outlinks || [];
        
        // Only include if there are outgoing links
        if (outlinks.length > 0) {
            filesWithLinks.push({
                name: page.file.name,
                outgoingLinks: outlinks.length
            });
        }
    } catch (error) {
        // Skip any files that cause errors
    }
});

// Sort by outgoing link count in descending order and take top 20
filesWithLinks = filesWithLinks
    .sort((a, b) => b.outgoingLinks - a.outgoingLinks)
    .slice(0, 20);

// Format the data for chart
const labels = filesWithLinks.map(file => `"${file.name}"`).join(", ");
const data = filesWithLinks.map(file => file.outgoingLinks).join(", ");

// Display the chart
dv.paragraph(`\`\`\`chart
    type: bar
    labels: [${labels}]
    series:
    - title: Outgoing Links
      data: [${data}]
    yTitle: Number of Outgoing Links
    width: 100%
    height: 500px
    yMin: 0
\`\`\``);

```





# Most used words
```chartsview
#-----------------#
#- chart type    -#
#-----------------#
type: WordCloud

#-----------------#
#- chart data    -#
#-----------------#
data: "wordcount:3_Explore Future Possibilities"

#-----------------#
#- chart options -#
#-----------------#
options:
  wordField: "word"
  weightField: "count"
  wordStyle:
    rotation: 0
```
