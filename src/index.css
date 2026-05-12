@import url('https://fonts.googleapis.com/css2?family=Montserrat:wght@300;400;500;600;700;800&family=Outfit:wght@300;400;500;600;700;800;900&family=JetBrains+Mono:wght@400;500;600;700&display=swap');
@import "tailwindcss";

@theme {
  --font-sans: "Outfit", "Montserrat", ui-sans-serif, system-ui, sans-serif;
  --font-mono: "JetBrains Mono", ui-monospace, SFMono-Regular, monospace;
}

@layer base {
  body {
    @apply antialiased text-slate-800 bg-slate-50;
  }
}

@layer utilities {
  .custom-scrollbar::-webkit-scrollbar {
    width: 6px;
    height: 6px;
  }

  .custom-scrollbar::-webkit-scrollbar-track {
    background: transparent;
  }

  .custom-scrollbar::-webkit-scrollbar-thumb {
    @apply bg-slate-200 rounded-full hover:bg-slate-300 transition-colors;
  }
}

@media print {
  aside, 
  header, 
  .no-print,
  button:not(.print-only) {
    display: none !important;
  }
  
  main {
    padding: 0 !important;
    margin: 0 !important;
    overflow: visible !important;
  }

  .print-area {
    width: 100% !important;
    box-shadow: none !important;
    border: none !important;
    padding: 0 !important;
  }

  table {
    border-collapse: collapse !important;
  }

  th, td {
    border: 1px solid #e2e8f0 !important;
    font-size: 10pt !important;
  }

  .custom-scrollbar {
    overflow: visible !important;
  }
}
