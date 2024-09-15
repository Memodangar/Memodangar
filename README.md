// script.js

const pdfjsLib = window['pdfjs-dist/build/pdf'];
pdfjsLib.GlobalWorkerOptions.workerSrc = 'https://mozilla.github.io/pdf.js/build/pdf.worker.js';

const fileInput = document.getElementById('fileInput');
const adjustMarginsButton = document.getElementById('adjustMargins');
const pdfContainer = document.getElementById('pdfContainer');

function renderPDF(pdfData) {
    pdfjsLib.getDocument({ data: pdfData }).promise.then(pdf => {
        pdfContainer.innerHTML = ''; // Clear previous content

        for (let pageNum = 1; pageNum <= pdf.numPages; pageNum++) {
            pdf.getPage(pageNum).then(page => {
                const scale = 1.5;
                const viewport = page.getViewport({ scale });

                const canvas = document.createElement('canvas');
                const context = canvas.getContext('2d');

                canvas.width = viewport.width;
                canvas.height = viewport.height;

                pdfContainer.appendChild(canvas);

                const renderContext = {
                    canvasContext: context,
                    viewport: viewport
                };
                page.render(renderContext);
            });
        }
    });
}

fileInput.addEventListener('change', (event) => {
    const file = event.target.files[0];
    if (file) {
        const reader = new FileReader();
        reader.onload = function(e) {
            renderPDF(e.target.result);
        };
        reader.readAsArrayBuffer(file);
    }
});

adjustMarginsButton.addEventListener('click', () => {
    const canvases = pdfContainer.getElementsByTagName('canvas');
    for (const canvas of canvases) {
        canvas.style.margin = '20px'; // Example margin adjustment
    }
});
