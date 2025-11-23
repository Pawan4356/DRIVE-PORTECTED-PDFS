###### _DISCLAIMER: This script is provided strictly for educational and personal-learning purposes. It must NOT be used to bypass copyright protections, access restricted content, circumvent view-only security measures, or download materials you do not own or do not have explicit permission to save. Always respect copyright laws, website terms of service, and the rights of content owners. The author of this script is not responsible for misuse._

```js
let jspdf = document.createElement("script");
jspdf.onload = function () {
    // A5 landscape dimensions in points (1 inch = 72 points)
    // Can be adjusted for A4 sized papers.
    const width = 420;   // 148 mm × 2.8349 ≈ 420 pt
    const height = 297.6; // 105 mm × 2.8349 ≈ 298 pt (jsPDF uses 297.6 for A5 landscape height)

    let pdf = new jsPDF({
        orientation: 'landscape',
        unit: 'pt',
        format: [width, height]  // A5 landscape in points
    });

    let elements = document.getElementsByTagName("img");
    let validImages = [];

    // Filter only blob images
    for (let i = 0; i < elements.length; i++) {
        let img = elements[i];
        if (/^blob:/.test(img.src) && img.naturalWidth > 0 && img.naturalHeight > 0) {
            validImages.push(img);
        }
    }

    if (validImages.length === 0) {
        alert("No blob images found on the page.");
        return;
    }

    let loaded = 0;
    function addImageToPdf(img) {
        let canvas = document.createElement('canvas');
        let ctx = canvas.getContext('2d');
        canvas.width = img.naturalWidth;
        canvas.height = img.naturalHeight;
        ctx.drawImage(img, 0, 0);

        let imgData = canvas.toDataURL('image/jpeg', 0.95);

        // Calculate scaling to fit image inside A5 page with small margin
        let margin = 20; // pt
        let maxWidth = width - 2 * margin;
        let maxHeight = height - 2 * margin;

        let scale = Math.min(maxWidth / img.naturalWidth, maxHeight / img.naturalHeight, 1);
        let w = img.naturalWidth * scale;
        let h = img.naturalHeight * scale;
        let x = (width - w) / 2;
        let y = (height - h) / 2;

        pdf.addImage(imgData, 'JPEG', x, y, w, h);

        loaded++;
        if (loaded < validImages.length) {
            pdf.addPage();
            addImageToPdf(validImages[loaded]);
        } else {
            pdf.save("slides_a5_landscape.pdf");
        }
    }

    // Start with first image
    addImageToPdf(validImages[0]);
};

jspdf.src = 'https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js';
document.head.appendChild(jspdf);
```
