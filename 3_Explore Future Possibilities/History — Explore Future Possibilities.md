---
created: 2025-02-24T15:38
updated: 2025-02-25T12:51
cssclasses:
  - wide-page
banner: Archive/Assets/03.png
sticker: lucide//clock-2
banner_y: "49"
---


> [!multi-column]
> >[!Summary]+ Latest Laura's Notes
> > ```dataview
> > table without id file.link as Notes, file.mtime as "Last Updated" 
> > from "Feeding Healthy Futures/3_Explore Future Possibilities"
> > where contains(author, "Laura DeOliveira")
> > sort file.mtime desc
> > limit 10
> > ```
> 
> >[!Summary]+ Latest Taahira's Notes
> > ```dataview
> > table without id file.link as Notes, file.mtime as "Last Updated"
> > from "Feeding Healthy Futures/3_Explore Future Possibilities"
> > where contains(author, "Taahira Ayoob")
> > sort file.mtime desc
> > limit 10
> > ```


>[!Example]+ All Current Reality and Urgencies Notes
> ```dataview
> table without id file.link as Note, author as Author, file.mtime as "Last Updated", file.ctime as "Created"
> from "Feeding Healthy Futures/3_Explore Future Possibilities"
> sort file.mtime desc
> ```
