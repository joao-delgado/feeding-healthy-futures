---
banner: Publish/feeding.jpg
created: 2025-02-25T19:43
updated: 2025-02-26T16:19
sticker: lucide//bar-chart-4
cssclasses:
  - wide-page
---

--- start-multi-column: Dashboard_Current_Reality_ and_Urgencies
```column-settings 
number of columns: 3
Border: disabled  
Shadow: off
```

```dataviewjs
const notes = dv.pages().filter(p => p.file.path.includes("Feeding Healthy Futures/")); 

dv.paragraph(`
<div style="border: solid 1px lightgrey; padding: 10px 20px; border-radius: 10px">
	<h1 style="font-size:3rem">${notes.length}</h1>
	<p>Total Notes</p>

</div>`)
```

--- end-column ---

```dataviewjs
const pagesInFolder = dv.pages().filter(p => p.file.path.includes("Feeding Healthy Futures/")).array();
const totalLinks = pagesInFolder.reduce((sum, page) => sum + (page.file.outlinks && page.file.outlinks.length ? page.file.outlinks.length : 0), 0);

dv.paragraph(`
<div style="border: solid 1px lightgrey; padding: 10px 20px; border-radius: 10px">
	<h1 style="font-size:3rem">${totalLinks}</h1>
	<p>Total Links</p>

</div>`)

```

--- end-column ---

```dataviewjs
// Get folder notes
const notes = dv.pages().filter(p => p.file.path.includes("Feeding Healthy Futures/"));

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






# Notes and Links created per Week
```dataviewjs
// Get all pages in the vault
const pages = dv.pages();
const dataByWeek = {};

// Month names for formatting
const months = ["Jan", "Feb", "Mar", "Apr", "May", "Jun", "Jul", "Aug", "Sep", "Oct", "Nov", "Dec"];

// Process each page to collect data
pages.forEach(page => {
    // Skip if file doesn't have a creation date
    if (!page.file.cday) return;
    
    // Get the creation date
    const date = new Date(page.file.cday);
    const year = date.getFullYear();
    const month = date.getMonth();
    
    // Determine which week of the month (1-4) this date falls into
    // Simplified approach: divide the days into 4 equal parts
    const dayOfMonth = date.getDate();
    const daysInMonth = new Date(year, month + 1, 0).getDate();
    const weekOfMonth = Math.min(Math.ceil(dayOfMonth / (daysInMonth / 4)), 4);
    
    // Format the week key: "Jan 2025 - W01"
    const weekKey = `${months[month]} ${year} - W${String(weekOfMonth).padStart(2, '0')}`;
    
    // Create sort key for proper ordering
    const sortKey = `${year}-${String(month+1).padStart(2, '0')}-${weekOfMonth}`;
    
    // Initialize or update the data for this week
    if (!dataByWeek[weekKey]) {
        dataByWeek[weekKey] = {
            notesCount: 0,
            linksCount: 0,
            sortKey: sortKey
        };
    }
    
    // Count this note
    dataByWeek[weekKey].notesCount++;
    
    // Count outgoing links if they exist
    if (page.file.outlinks) {
        dataByWeek[weekKey].linksCount += page.file.outlinks.length;
    }
});

// Get only weeks that have data and sort them chronologically
const weeksWithData = Object.keys(dataByWeek)
    .filter(weekKey => dataByWeek[weekKey].notesCount > 0 || dataByWeek[weekKey].linksCount > 0)
    .sort((a, b) => dataByWeek[a].sortKey.localeCompare(dataByWeek[b].sortKey));

// Prepare data for chart
const labels = weeksWithData.map(week => `"${week}"`).join(", ");
const notesData = weeksWithData.map(week => dataByWeek[week].notesCount).join(", ");
const linksData = weeksWithData.map(week => dataByWeek[week].linksCount).join(", ");

// Create chart
dv.paragraph(`\`\`\`chart
    type: line
    labels: [${labels}]
    series:
      - title: Notes Created
        data: [${notesData}]
      - title: Outgoing Links
        data: [${linksData}]
    tension: 0.3
    legend: true
    yTitle: "Count"
    xTitle: "Week"
    labelRotation: 45
\`\`\``);
```





--- start-multi-column: Dashboard_Current_Reality_ and_Urgencies
```column-settings 
number of columns: 2
Border: disabled  
Shadow: off
```

# Tags Usage Percentage
```dataviewjs
// Define the folder to search in
const folderPath = "Feeding Healthy Futures";
// Get all files in the folder
const files = dv.pages(`"${folderPath}"`);

// Object to store tag counts
const tagCounts = {
    "Past": 0,
    "Present": 0,
    "Future": 0,
    "Transitions": 0
};

// Count occurrences of each tag
files.forEach(file => {
    if (file.file.tags) {
        file.file.tags.forEach(tag => {
            // Remove the # and convert to normal case for comparison
            const cleanTag = tag.replace("#", "");
            
            // Check if the tag matches any of our target tags
            if (tagCounts.hasOwnProperty(cleanTag)) {
                tagCounts[cleanTag]++;
            }
        });
    }
});

// Calculate total for percentage calculation
const total = Object.values(tagCounts).reduce((sum, count) => sum + count, 0);

// Create labels with percentages
const labelsWithPercentages = Object.entries(tagCounts).map(([tag, count]) => {
    const percentage = total > 0 ? ((count / total) * 100).toFixed(1) : 0;
    return `"${tag} (${percentage}%)"`;
}).join(", ");

const data = Object.values(tagCounts).join(", ");

// Create and display the pie chart using the Obsidian Charts plugin
dv.paragraph(`\`\`\`chart
    type: pie
    labels: [${labelsWithPercentages}]
    series:
    - title: Tag Usage
      data: [${data}]
    labelColors: true
\`\`\``);
```

--- end-column ---

# Tags Usage Amounts
```dataviewjs
// Define folder path and tags to track
const folderPath = "Feeding Healthy Futures";
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





# Total Notes per Research Phase
```dataviewjs
// Main folder path
const mainFolderPath = "Feeding Healthy Futures";

// Define the specific folders to count notes in (just the subfolder names)
const specificFolders = [
    "1_Current Reality and Urgencies",
    "2_Analyse Future Predictions", 
    "3_Explore Future Possibilities", 
    "4_Balanced Future Filter",
    "5_New Futures"
];

// Object to store folder counts
const folderCounts = {};

// Initialize counts for all specified folders
specificFolders.forEach(folder => {
    folderCounts[folder] = 0;
});

// Get all files from the main folder
const allFiles = dv.pages(`"${mainFolderPath}"`);

// Count files in each specified folder
allFiles.forEach(file => {
    // Check if the file is in one of our target folders
    for (const folder of specificFolders) {
        const fullFolderPath = `${mainFolderPath}/${folder}`;
        if (file.file.folder.startsWith(fullFolderPath)) {
            folderCounts[folder]++;
            break;
        }
    }
});

// Prepare data for the chart
const labels = Object.keys(folderCounts).map(label => `"${label}"`).join(", ");
const data = Object.values(folderCounts).join(", ");

// Create and display the polar area chart
dv.paragraph(`\`\`\`chart
    type: polarArea
    labels: [${labels}]
    series:
    - title: Notes Count
      data: [${data}]
    labelColors: true
    width: 60%
\`\`\``);
```



# Top 20 notes with the most links
```dataviewjs
// Define the folder path - make sure this matches your folder structure exactly
const folderPath = "Feeding Healthy Futures";

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
data: "wordcount:Feeding Healthy Futures/"

#-----------------#
#- chart options -#
#-----------------#
options:
  wordField: "word"
  weightField: "count"
  wordStyle:
    rotation: 0
```
