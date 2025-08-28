import streamlit as st
import streamlit.components.v1 as components

st.set_page_config(page_title="Compare Two Files", layout="wide")

HTML_DOC = r"""
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Compare Two Files</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/xlsx/0.18.5/xlsx.full.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/FileSaver.js/2.0.5/FileSaver.min.js"></script>
    <style>
/*
 * Google Fonts: Inter for a professional and clean look
 */
 @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap');

 /*
  * ======================================================================
  * Advanced Dark Theme Variables
  * ======================================================================
  */
 :root {
     /* Dark Background Gradients */
     --primary-bg: linear-gradient(135deg, #0f1419 0%, #1a202c 50%, #2d3748 100%);
     --secondary-bg: linear-gradient(145deg, #1e293b 0%, #334155 100%);
     --tertiary-bg: linear-gradient(135deg, #1f2937 0%, #374151 100%);
     
     /* Glow Colors */
     --primary-glow: #00d4ff;
     --secondary-glow: #7c3aed;
     --success-glow: #10b981;
     --warning-glow: #f59e0b;
     --error-glow: #ef4444;
     
     /* Enhanced Glow Effects */
     --neon-cyan: rgba(0, 212, 255, 0.6);
     --neon-purple: rgba(124, 58, 237, 0.6);
     --neon-green: rgba(16, 185, 129, 0.6);
     --neon-blue: rgba(59, 130, 246, 0.6);
     --neon-pink: rgba(236, 72, 153, 0.6);
     
     /* Text Colors */
     --text-primary: #f8fafc;
     --text-secondary: #e2e8f0;
     --text-muted: #94a3b8;
     --text-accent: var(--primary-glow);
     
     /* Advanced Shadows */
     --shadow-glow: 0 0 20px rgba(0, 212, 255, 0.3), 0 0 40px rgba(0, 212, 255, 0.1);
     --shadow-deep: 0 25px 50px -12px rgba(0, 0, 0, 0.8);
     --shadow-inset: inset 0 2px 4px 0 rgba(0, 0, 0, 0.3);
 }
 
 /*
  * ======================================================================
  * Advanced Base Styles
  * ======================================================================
  */
 * {
     box-sizing: border-box;
 }
 
 *::before,
 *::after {
     box-sizing: inherit;
 }
 
 html {
     scroll-behavior: smooth;
     overflow-x: hidden;
 }
 
 body {
     font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
     background: var(--primary-bg);
     color: var(--text-primary);
     margin: 0;
     padding: 0;
     min-height: 100vh;
     overflow-x: hidden;
     line-height: 1.6;
     font-weight: 400;
     -webkit-font-smoothing: antialiased;
     -moz-osx-font-smoothing: grayscale;
 }
 
 body::before {
     content: '';
     position: fixed;
     top: 0;
     left: 0;
     width: 100%;
     height: 100%;
     background: 
         radial-gradient(ellipse 80% 50% at 50% -20%, rgba(0, 212, 255, 0.15) 0%, transparent 50%),
         radial-gradient(ellipse 80% 50% at 50% 120%, rgba(124, 58, 237, 0.15) 0%, transparent 50%);
     pointer-events: none;
     z-index: -1;
 }
 
 /*
  * ======================================================================
  * Advanced Typography with Glowing Effects
  * ======================================================================
  */
 .glow-text-primary {
     color: var(--primary-glow);
     text-shadow: 
         0 0 5px var(--neon-cyan),
         0 0 10px var(--neon-cyan),
         0 0 20px var(--neon-cyan),
         0 0 40px var(--neon-cyan);
     animation: textPulse 3s ease-in-out infinite;
     font-weight: 700;
     letter-spacing: -0.02em;
 }
 
 .glow-text-secondary {
     color: var(--text-secondary);
     text-shadow: 
         0 0 3px var(--neon-cyan),
         0 0 6px rgba(0, 212, 255, 0.3);
     font-weight: 500;
 }
 
 .glow-text-accent {
     background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%);
     -webkit-background-clip: text;
     background-clip: text;
     -webkit-text-fill-color: transparent;
     text-shadow: none;
     font-weight: 600;
     position: relative;
 }
 
 .glow-text-accent::after {
     content: attr(data-text);
     position: absolute;
     top: 0;
     left: 0;
     z-index: -1;
     background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%);
     -webkit-background-clip: text;
     background-clip: text;
     -webkit-text-fill-color: transparent;
     filter: blur(8px);
     opacity: 0.7;
 }
 
 /*
  * ======================================================================
  * Advanced Container Styling
  * ======================================================================
  */
 .neo-container {
     background: var(--secondary-bg);
     backdrop-filter: blur(20px);
     border-radius: 24px;
     border: 1px solid rgba(255, 255, 255, 0.1);
     box-shadow: 
         var(--shadow-deep),
         var(--shadow-glow),
         var(--shadow-inset);
     position: relative;
     overflow: hidden;
     transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
 }
 
 .neo-container::before {
     content: '';
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     height: 1px;
     background: linear-gradient(90deg, transparent, var(--primary-glow), transparent);
     opacity: 0.5;
 }
 
 .neo-container:hover {
     transform: translateY(-8px);
     box-shadow: 
         0 35px 70px -12px rgba(0, 0, 0, 0.9),
         0 0 30px var(--neon-cyan),
         0 0 60px rgba(0, 212, 255, 0.2),
         var(--shadow-inset);
     border-color: rgba(0, 212, 255, 0.3);
 }
 
 /*
  * ======================================================================
  * Advanced Button Styling
  * ======================================================================
  */
 .neo-button {
     background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%);
     border: none;
     border-radius: 16px;
     padding: 16px 32px;
     font-family: inherit;
     font-weight: 600;
     font-size: 16px;
     color: #ffffff;
     cursor: pointer;
     position: relative;
     overflow: hidden;
     transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
     box-shadow: 
         0 8px 32px rgba(0, 212, 255, 0.3),
         0 0 0 1px rgba(255, 255, 255, 0.1);
     text-transform: uppercase;
     letter-spacing: 0.5px;
 }
 
 .neo-button::before {
     content: '';
     position: absolute;
     top: 0;
     left: -100%;
     width: 100%;
     height: 100%;
     background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.4), transparent);
     transition: left 0.5s;
 }
 
 .neo-button:hover::before {
     left: 100%;
 }
 
 .neo-button:hover {
     transform: translateY(-2px) scale(1.02);
     box-shadow: 
         0 12px 48px rgba(0, 212, 255, 0.4),
         0 0 20px var(--neon-cyan),
         0 0 40px rgba(0, 212, 255, 0.3),
         0 0 0 1px rgba(255, 255, 255, 0.2);
 }
 
 .neo-button:active {
     transform: translateY(0) scale(0.98);
 }
 
 .neo-button:disabled {
     background: linear-gradient(135deg, #374151 0%, #4b5563 100%);
     color: #9ca3af;
     cursor: not-allowed;
     box-shadow: none;
     transform: none;
 }
 
 .neo-button:disabled::before {
     display: none;
 }
 
 /*
  * ======================================================================
  * Advanced File Upload Styling
  * ======================================================================
  */
 .neo-upload {
     background: var(--tertiary-bg);
     border: 2px dashed rgba(0, 212, 255, 0.3);
     border-radius: 20px;
     padding: 32px 24px;
     text-align: center;
     position: relative;
     overflow: hidden;
     transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
     cursor: pointer;
 }
 
 .neo-upload::before {
     content: '';
     position: absolute;
     top: 50%;
     left: 50%;
     width: 0;
     height: 0;
     background: radial-gradient(circle, var(--neon-cyan) 0%, transparent 70%);
     opacity: 0;
     transform: translate(-50%, -50%);
     transition: all 0.4s ease;
     border-radius: 50%;
 }
 
 .neo-upload:hover::before {
     width: 300px;
     height: 300px;
     opacity: 0.1;
 }
 
 .neo-upload:hover {
     border-color: var(--primary-glow);
     transform: translateY(-4px);
     box-shadow: 
         0 20px 40px rgba(0, 0, 0, 0.4),
         0 0 20px var(--neon-cyan);
 }
 
 .neo-upload.active {
     border-color: var(--success-glow);
     background: linear-gradient(135deg, rgba(16, 185, 129, 0.1) 0%, rgba(5, 150, 105, 0.05) 100%);
     box-shadow: 0 0 20px var(--neon-green);
 }
 
 .neo-upload.disabled {
     opacity: 0.5;
     cursor: not-allowed;
     border-style: dotted;
     transform: none;
     background: rgba(55, 65, 81, 0.3);
 }
 
 .neo-upload.disabled:hover {
     border-color: rgba(0, 212, 255, 0.3);
     transform: none;
     box-shadow: none;
 }
 
 .neo-upload.disabled:hover::before {
     width: 0;
     height: 0;
     opacity: 0;
 }
 
 /*
  * ======================================================================
  * Advanced Statistics Cards
  * ======================================================================
  */
 .neo-stat {
     background: var(--tertiary-bg);
     border-radius: 20px;
     padding: 24px;
     text-align: center;
     position: relative;
     overflow: hidden;
     border: 1px solid rgba(255, 255, 255, 0.1);
     transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
 }
 
 .neo-stat::before {
     content: '';
     position: absolute;
     top: 0;
     left: -100%;
     width: 100%;
     height: 100%;
     background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.1), transparent);
     transition: left 0.6s;
 }
 
 .neo-stat:hover::before {
     left: 100%;
 }
 
 .neo-stat:hover {
     transform: translateY(-8px) scale(1.05);
     box-shadow: 
         0 20px 40px rgba(0, 0, 0, 0.3),
         0 0 20px currentColor;
 }
 
 .neo-stat-number {
     font-size: 2.5rem;
     font-weight: 800;
     margin-bottom: 8px;
     text-shadow: 
         0 0 10px currentColor,
         0 0 20px currentColor,
         0 0 30px currentColor;
     animation: numberGlow 2s ease-in-out infinite alternate;
 }
 
 .neo-stat-label {
     font-size: 0.875rem;
     color: var(--text-muted);
     font-weight: 500;
     text-transform: uppercase;
     letter-spacing: 0.5px;
 }
 
 /*
  * ======================================================================
  * Advanced Table Styling
  * ======================================================================
  */
 .neo-table {
     background: var(--tertiary-bg);
     border-radius: 16px;
     overflow: hidden;
     border: 1px solid rgba(255, 255, 255, 0.1);
     box-shadow: var(--shadow-deep);
 }
 
 .neo-table thead {
     background: linear-gradient(135deg, rgba(0, 212, 255, 0.2) 0%, rgba(124, 58, 237, 0.2) 100%);
     backdrop-filter: blur(10px);
 }
 
 .neo-table th {
     padding: 20px 16px;
     font-weight: 600;
     color: var(--primary-glow);
     text-transform: uppercase;
     letter-spacing: 0.5px;
     font-size: 0.875rem;
     text-shadow: 0 0 10px var(--neon-cyan);
 }
 
 .neo-table td {
     padding: 16px;
     border-bottom: 1px solid rgba(255, 255, 255, 0.05);
     transition: all 0.3s ease;
     color: var(--text-secondary);
 }
 
 .neo-table tbody tr:hover {
     background: rgba(0, 212, 255, 0.05);
     box-shadow: inset 0 0 20px rgba(0, 212, 255, 0.1);
 }
 
 .neo-table tbody tr:hover td {
     color: var(--text-primary);
     text-shadow: 0 0 5px rgba(0, 212, 255, 0.3);
 }
 
 /*
  * ======================================================================
  * Advanced Form Elements
  * ======================================================================
  */
 
 /* File path input styling */
 .neo-upload input[type="text"] {
     background: rgba(31, 41, 55, 0.8);
     border: 1px solid rgba(75, 85, 99, 0.6);
     color: var(--text-primary);
     font-size: 0.875rem;
     padding: 0.5rem 0.75rem;
     border-radius: 0.5rem;
     transition: all 0.3s ease;
     backdrop-filter: blur(8px);
     min-width: 0;
     flex: 1;
 }
 
 .neo-upload input[type="text"]:focus {
     outline: none;
     border-color: var(--primary-glow);
     box-shadow: 0 0 0 2px rgba(0, 212, 255, 0.2);
     background: rgba(31, 41, 55, 0.9);
 }
 
 .neo-upload input[type="text"]::placeholder {
     color: var(--text-muted);
     opacity: 0.7;
 }
 
 /* File path load buttons */
 .neo-upload button[data-path-load] {
     background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%);
     border: none;
     color: white;
     font-weight: 500;
     text-transform: uppercase;
     letter-spacing: 0.025em;
     transition: all 0.3s ease;
     box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
     position: relative;
     overflow: hidden;
     min-width: 80px;
     white-space: nowrap;
     flex-shrink: 0;
 }
 
 .neo-upload button[data-path-load]:hover {
     transform: translateY(-2px);
     box-shadow: 
         0 8px 25px rgba(0, 0, 0, 0.4),
         0 0 20px var(--primary-glow);
 }
 
 .neo-upload button[data-path-load]:active {
     transform: translateY(0);
 }
 
 .neo-upload button[data-path-load]:disabled {
     opacity: 0.6;
     cursor: not-allowed;
     transform: none;
 }
 
 /* Color variations for different file types */
 .neo-upload[data-step="1"] button[data-path-load],
 .neo-upload[data-step="2"] button[data-path-load] {
     background: linear-gradient(135deg, #06b6d4 0%, #0891b2 100%);
 }
 
 .neo-upload[data-step="3"] button[data-path-load],
 .neo-upload[data-step="4"] button[data-path-load] {
     background: linear-gradient(135deg, #10b981 0%, #059669 100%);
 }
 
 .neo-upload[data-step="5"] button[data-path-load] {
     background: linear-gradient(135deg, #8b5cf6 0%, #7c3aed 100%);
 }
 
 /* File path section styling */
 .neo-upload .border-t {
     border-color: rgba(75, 85, 99, 0.4) !important;
 }
 
 .neo-upload label {
     color: var(--text-muted);
     font-weight: 500;
 }
 
 /* Disabled state for file path inputs */
 .neo-upload.disabled input[type="text"] {
     opacity: 0.5;
     cursor: not-allowed;
     background: rgba(55, 65, 81, 0.3);
 }
 
 .neo-upload.disabled button[data-path-load] {
     opacity: 0.3;
     cursor: not-allowed;
     background: rgba(75, 85, 99, 0.5);
 }
 
 .neo-upload.disabled button[data-path-load]:hover {
     transform: none;
     box-shadow: none;
 }
 
 /* Checkbox styling */
 .neo-checkbox {
     appearance: none;
     width: 20px;
     height: 20px;
     border: 2px solid rgba(0, 212, 255, 0.4);
     border-radius: 4px;
     background: rgba(31, 41, 55, 0.8);
     position: relative;
     cursor: pointer;
     transition: all 0.3s ease;
 }
 
 .neo-checkbox:checked {
     background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%);
     border-color: var(--primary-glow);
     box-shadow: 
         0 0 0 3px rgba(0, 212, 255, 0.2),
         0 0 10px var(--neon-cyan);
 }
 
 .neo-checkbox:checked::before {
     content: '✓';
     position: absolute;
     top: 50%;
     left: 50%;
     transform: translate(-50%, -50%);
     color: #ffffff;
     font-weight: 700;
     font-size: 12px;
     text-shadow: 0 0 5px rgba(255, 255, 255, 0.8);
 }
 
 .neo-checkbox:focus {
     outline: none;
     box-shadow: 0 0 0 3px rgba(0, 212, 255, 0.4);
 }
 
 /*
  * ======================================================================
  * Advanced Loading Animation
  * ======================================================================
  */
 .neo-loader {
     position: relative;
     width: 80px;
     height: 80px;
 }
 
 .neo-loader::before,
 .neo-loader::after {
     content: '';
     position: absolute;
     border-radius: 50%;
     animation: pulsate 2s ease-in-out infinite;
 }
 
 .neo-loader::before {
     width: 100%;
     height: 100%;
     border: 4px solid var(--primary-glow);
     box-shadow: 
         0 0 20px var(--neon-cyan),
         0 0 40px var(--neon-cyan),
         0 0 60px var(--neon-cyan);
     animation-delay: 0s;
 }
 
 .neo-loader::after {
     width: 60%;
     height: 60%;
     top: 20%;
     left: 20%;
     border: 3px solid var(--secondary-glow);
     box-shadow: 
         0 0 15px var(--neon-purple),
         0 0 30px var(--neon-purple);
     animation-delay: 0.5s;
 }
 
 /*
  * ======================================================================
  * Advanced Animations
  * ======================================================================
  */
 @keyframes textPulse {
     0%, 100% {
         text-shadow: 
             0 0 5px var(--neon-cyan),
             0 0 10px var(--neon-cyan),
             0 0 20px var(--neon-cyan);
     }
     50% {
         text-shadow: 
             0 0 10px var(--neon-cyan),
             0 0 20px var(--neon-cyan),
             0 0 30px var(--neon-cyan),
             0 0 40px var(--neon-cyan);
     }
 }
 
 @keyframes numberGlow {
     0% {
         text-shadow: 
             0 0 10px currentColor,
             0 0 20px currentColor;
         transform: scale(1);
     }
     100% {
         text-shadow: 
             0 0 15px currentColor,
             0 0 30px currentColor,
             0 0 45px currentColor;
         transform: scale(1.02);
     }
 }
 
 @keyframes pulsate {
     0% {
         transform: scale(0.8);
         opacity: 1;
     }
     100% {
         transform: scale(1.2);
         opacity: 0;
     }
 }
 
 @keyframes shimmer {
     0% {
         background-position: -200% 0;
     }
     100% {
         background-position: 200% 0;
     }
 }
 
 @keyframes floatUp {
     0% {
         opacity: 0;
         transform: translateY(30px);
     }
     100% {
         opacity: 1;
         transform: translateY(0);
     }
 }
 
 /*
  * ======================================================================
  * Enhanced Responsive Design
  * ======================================================================
  */
 @media (max-width: 1024px) {
     .neo-container {
         margin: 16px;
         border-radius: 20px;
     }
     
     .neo-button {
         padding: 14px 28px;
         font-size: 15px;
     }
 }
 
 @media (max-width: 768px) {
     .neo-container {
         margin: 12px;
         padding: 20px;
         border-radius: 16px;
     }
     
     .neo-stat-number {
         font-size: 2rem;
     }
     
     .neo-button {
         padding: 12px 24px;
         font-size: 14px;
     }
     
     .neo-upload {
         padding: 24px 16px;
     }
 }
 
 @media (max-width: 480px) {
     .glow-text-primary {
         font-size: 1.75rem;
     }
     
     .neo-stat {
         padding: 16px;
     }
     
     .neo-stat-number {
         font-size: 1.75rem;
     }
 }
 
 /*
  * ======================================================================
  * Advanced Utility Classes
  * ======================================================================
  */
 .animate-float-up {
     animation: floatUp 0.6s ease-out;
 }
 
 .text-glow-cyan {
     color: var(--primary-glow);
     text-shadow: 0 0 10px var(--neon-cyan);
 }
 
 .text-glow-green {
     color: var(--success-glow);
     text-shadow: 0 0 10px var(--neon-green);
 }
 
 .text-glow-purple {
     color: var(--secondary-glow);
     text-shadow: 0 0 10px var(--neon-purple);
 }
 
 .border-glow {
     border: 1px solid rgba(0, 212, 255, 0.3);
     box-shadow: 0 0 20px rgba(0, 212, 255, 0.2);
 }
 
 .bg-glass {
     background: rgba(255, 255, 255, 0.05);
     backdrop-filter: blur(20px);
     border: 1px solid rgba(255, 255, 255, 0.1);
 }
 
 /*
  * ======================================================================
  * Enhanced Tailwind Overrides
  * ======================================================================
  */
 .bg-gray-50 { background: var(--tertiary-bg) !important; }
 .bg-gray-100 { background: rgba(255, 255, 255, 0.05) !important; }
 .bg-gray-800 { background: var(--secondary-bg) !important; }
 .bg-gray-900 { background: var(--primary-bg) !important; }
 
 .text-gray-300 { color: var(--text-secondary) !important; }
 .text-gray-400 { color: var(--text-muted) !important; }
 .text-gray-500 { color: var(--text-muted) !important; }
 .text-gray-600 { color: #64748b !important; }
 .text-gray-700 { color: #475569 !important; }
 .text-gray-800 { color: var(--text-secondary) !important; }
 .text-gray-900 { color: var(--text-primary) !important; }
 
 .text-cyan-400 { color: var(--primary-glow) !important; }
 .text-blue-400 { color: #60a5fa !important; }
 .text-green-400 { color: var(--success-glow) !important; }
 .text-purple-400 { color: var(--secondary-glow) !important; }
 .text-red-400 { color: var(--error-glow) !important; }
 .text-yellow-400 { color: var(--warning-glow) !important; }
 
 .border-gray-600 { border-color: rgba(255, 255, 255, 0.1) !important; }
 .border-cyan-500 { border-color: rgba(0, 212, 255, 0.3) !important; }
 
 .divide-gray-600 > :not([hidden]) ~ :not([hidden]) {
     border-color: rgba(255, 255, 255, 0.05) !important;
 }
 
 .shadow-xl { box-shadow: var(--shadow-deep) !important; }
 .shadow-2xl { 
     box-shadow: 
         var(--shadow-deep),
         0 0 20px rgba(0, 212, 255, 0.1) !important; 
 }
 
 /* Legacy compatibility - replace @extend with actual classes */
 .glow-text { 
     color: var(--primary-glow);
     text-shadow: 
         0 0 5px var(--neon-cyan),
         0 0 10px var(--neon-cyan),
         0 0 20px var(--neon-cyan),
         0 0 40px var(--neon-cyan);
     animation: textPulse 3s ease-in-out infinite;
     font-weight: 700;
     letter-spacing: -0.02em;
 }
 
 .glow-box { 
     background: var(--secondary-bg);
     backdrop-filter: blur(20px);
     border-radius: 24px;
     border: 1px solid rgba(255, 255, 255, 0.1);
     box-shadow: 
         var(--shadow-deep),
         var(--shadow-glow),
         var(--shadow-inset);
     position: relative;
     overflow: hidden;
     transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
 }
 
 .glow-box::before {
     content: '';
     position: absolute;
     top: 0;
     left: 0;
     right: 0;
     height: 1px;
     background: linear-gradient(90deg, transparent, var(--primary-glow), transparent);
     opacity: 0.5;
 }
 
 .glow-box:hover {
     transform: translateY(-8px);
     box-shadow: 
         0 35px 70px -12px rgba(0, 0, 0, 0.9),
         0 0 30px var(--neon-cyan),
         0 0 60px rgba(0, 212, 255, 0.2),
         var(--shadow-inset);
     border-color: rgba(0, 212, 255, 0.3);
 }
 
 .glow-button { 
     background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%);
     border: none;
     border-radius: 16px;
     padding: 16px 32px;
     font-family: inherit;
     font-weight: 600;
     font-size: 16px;
     color: #ffffff;
     cursor: pointer;
     position: relative;
     overflow: hidden;
     transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
     box-shadow: 
         0 8px 32px rgba(0, 212, 255, 0.3),
         0 0 0 1px rgba(255, 255, 255, 0.1);
     text-transform: uppercase;
     letter-spacing: 0.5px;
 }
 
 .glow-stat { 
     background: var(--tertiary-bg);
     border-radius: 20px;
     padding: 24px;
     text-align: center;
     position: relative;
     overflow: hidden;
     border: 1px solid rgba(255, 255, 255, 0.1);
     transition: all 0.4s cubic-bezier(0.4, 0, 0.2, 1);
 }
 
 .glow-number { 
    font-size: 2.5rem;
    font-weight: 800;
    margin-bottom: 8px;
    text-shadow: 
        0 0 10px currentColor,
        0 0 20px currentColor,
        0 0 30px currentColor;
    animation: numberGlow 2s ease-in-out infinite alternate;
}

/* Custom background colors for comparison panels */
.bg-gray-25 { 
    background-color: #fafafa; 
}

.bg-blue-25 { 
    background-color: #f0f8ff; 
}

.bg-green-25 { 
    background-color: #f0fff4; 
}

/* Dark theme highlighting for mismatched cells */
.mismatch-cell-dark {
    background: #fde047 !important;
    border-left: 3px solid #fbbf24 !important;
    color: #1f2937 !important;
    position: relative;
}

/* Dark theme row highlighting for mismatched rows */
.mismatch-row-dark {
    background: linear-gradient(135deg, #fef3c7 0%, #fde047 100%) !important;
    border-left: 2px solid #fbbf24 !important;
    color: #1f2937 !important;
}

/* Dark theme match highlighting */
.match-cell-dark {
    background: linear-gradient(135deg, #14532d 0%, #166534 100%) !important;
    border-left: 2px solid #22c55e !important;
}

.match-row-dark {
    background: linear-gradient(135deg, #052e16 0%, #14532d 100%) !important;
}

/* Dark theme custom backgrounds */
.bg-gray-850 {
    background-color: #1f2937;
}

/* Ensure exact row height matching */
.comparison-row {
    height: 40px;
    min-height: 40px;
    max-height: 40px;
}

/* Synchronized scroll containers */
.sync-scroll-container {
    overflow: auto;
    scrollbar-width: thin;
    scrollbar-color: rgba(156, 163, 175, 0.5) transparent;
}

.sync-scroll-container::-webkit-scrollbar {
    width: 8px;
    height: 8px;
}

.sync-scroll-container::-webkit-scrollbar-track {
    background: transparent;
}

.sync-scroll-container::-webkit-scrollbar-thumb {
    background-color: rgba(156, 163, 175, 0.5);
    border-radius: 4px;
}

.sync-scroll-container::-webkit-scrollbar-thumb:hover {
    background-color: rgba(156, 163, 175, 0.7);
}

/*
 * ======================================================================
 * Side-by-Side Comparison Styles
 * ======================================================================
 */

/* Side-by-side comparison container */
.side-by-side-comparison {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 1rem;
    min-height: 400px;
}

/* File panel styling */
.file-panel {
    background: var(--tertiary-bg);
    border-radius: 16px;
    overflow: hidden;
    border: 1px solid rgba(255, 255, 255, 0.1);
    display: flex;
    flex-direction: column;
    height: 500px;
}

/* Sticky headers for side-by-side */
.file-panel-header {
    position: sticky;
    top: 0;
    z-index: 10;
    background: linear-gradient(135deg, rgba(0, 212, 255, 0.2) 0%, rgba(124, 58, 237, 0.2) 100%);
    backdrop-filter: blur(10px);
    border-bottom: 1px solid rgba(255, 255, 255, 0.1);
}

.file-panel-header.file1 {
    background: linear-gradient(135deg, rgba(6, 182, 212, 0.2) 0%, rgba(59, 130, 246, 0.2) 100%);
}

.file-panel-header.file2 {
    background: linear-gradient(135deg, rgba(16, 185, 129, 0.2) 0%, rgba(5, 150, 105, 0.2) 100%);
}

/* Scrollable data area */
.file-panel-data {
    flex: 1;
    overflow: auto;
    scrollbar-width: thin;
    scrollbar-color: rgba(156, 163, 175, 0.3) transparent;
}

.file-panel-data::-webkit-scrollbar {
    width: 6px;
    height: 6px;
}

.file-panel-data::-webkit-scrollbar-track {
    background: transparent;
}

.file-panel-data::-webkit-scrollbar-thumb {
    background-color: rgba(156, 163, 175, 0.3);
    border-radius: 3px;
}

.file-panel-data::-webkit-scrollbar-thumb:hover {
    background-color: rgba(156, 163, 175, 0.5);
}

/* Cell mismatch highlighting for side-by-side */
.cell-mismatch {
    background: rgba(253, 224, 71, 0.3) !important;
    border: 1px solid rgba(251, 191, 36, 0.6) !important;
    box-shadow: 
        0 0 0 1px rgba(251, 191, 36, 0.4),
        inset 0 0 10px rgba(251, 191, 36, 0.2) !important;
    position: relative;
}

.cell-mismatch::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(45deg, transparent 30%, rgba(251, 191, 36, 0.1) 50%, transparent 70%);
    animation: mismatchPulse 2s ease-in-out infinite;
    pointer-events: none;
}

/* Account ID/Number mismatch highlighting */
.account-mismatch-row {
    border-left: 4px solid rgba(251, 191, 36, 0.8) !important;
    background: rgba(251, 191, 36, 0.1) !important;
}

/* Gap row styling */
.gap-row {
    background: rgba(107, 114, 128, 0.2) !important;
    border-top: 2px solid rgba(107, 114, 128, 0.5);
    border-bottom: 2px solid rgba(107, 114, 128, 0.5);
    height: 30px;
    min-height: 30px;
}

.gap-row td {
    padding: 8px 12px !important;
    text-align: center;
    font-style: italic;
    color: rgba(156, 163, 175, 0.8);
    font-size: 0.75rem;
}

/* Missing row styling */
.missing-row {
    background: rgba(253, 224, 71, 0.3) !important;
    border-left: 4px solid rgba(251, 191, 36, 0.8) !important;
}

.missing-row td {
    color: rgba(92, 92, 92, 0.9);
    font-style: italic;
    text-align: center;
}

/* Matched cell styling */
.cell-match {
    background: rgba(16, 185, 129, 0.1) !important;
    border: 1px solid rgba(16, 185, 129, 0.3) !important;
}

/* Row highlighting for better visual alignment */
.comparison-table tbody tr:nth-child(even) {
    background: rgba(255, 255, 255, 0.02);
}

.comparison-table tbody tr:nth-child(odd) {
    background: rgba(255, 255, 255, 0.01);
}

.comparison-table tbody tr:hover {
    background: rgba(0, 212, 255, 0.05) !important;
    box-shadow: inset 0 0 20px rgba(0, 212, 255, 0.1);
}

/* Side-by-side header alignment */
.aligned-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    min-height: 50px;
}

.aligned-header th {
    padding: 12px 16px;
    min-width: 100px;
    text-align: left;
    border-bottom: 2px solid rgba(255, 255, 255, 0.1);
}

/* Responsive adjustments for side-by-side */
@media (max-width: 1024px) {
    .side-by-side-comparison {
        grid-template-columns: 1fr;
        gap: 1.5rem;
    }
    
    .file-panel {
        height: 400px;
    }
}

/* Mismatch pulse animation */
@keyframes mismatchPulse {
    0%, 100% {
        opacity: 0.1;
    }
    50% {
        opacity: 0.3;
    }
}

/* Button state styling for controls */
.control-button-active {
    background: linear-gradient(135deg, var(--primary-glow) 0%, var(--secondary-glow) 100%) !important;
    box-shadow: 0 0 15px var(--neon-cyan) !important;
}

.control-button-inactive {
    background: linear-gradient(135deg, #374151 0%, #4b5563 100%) !important;
    color: #9ca3af !important;
    box-shadow: none !important;
}

/* Legend styling improvements */
.comparison-legend {
    background: rgba(31, 41, 55, 0.8);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(75, 85, 99, 0.3);
    border-radius: 12px;
    padding: 16px;
}

.legend-item {
    display: flex;
    align-items: center;
    gap: 8px;
    font-size: 0.75rem;
    color: var(--text-secondary);
}

.legend-color {
    width: 12px;
    height: 12px;
    border-radius: 3px;
    border: 1px solid rgba(255, 255, 255, 0.2);
}

/* File stats styling */
.file-stats {
    font-size: 0.75rem;
    color: rgba(226, 232, 240, 0.8);
    font-weight: 500;
}

/* Improved table cell styling for side-by-side */
.side-by-side-cell {
    padding: 8px 12px;
    border-bottom: 1px solid rgba(255, 255, 255, 0.05);
    font-size: 0.75rem;
    line-height: 1.4;
    max-width: 120px;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
    transition: all 0.2s ease;
}

.side-by-side-cell:hover {
    background: rgba(0, 212, 255, 0.1) !important;
    white-space: normal;
    word-wrap: break-word;
    max-width: none;
    z-index: 5;
    position: relative;
}

/* ======================================================================
 * Enhanced Side-by-Side Comparison Styles
 * ====================================================================== */

/* Enhanced gap row styling */
.gap-row {
    background: linear-gradient(90deg, rgba(245, 158, 11, 0.1) 0%, rgba(245, 158, 11, 0.2) 50%, rgba(245, 158, 11, 0.1) 100%);
    border: 1px solid rgba(245, 158, 11, 0.3);
    height: 40px !important;
}

.gap-row td {
    border: none !important;
    font-weight: 500;
    text-shadow: 0 0 10px rgba(245, 158, 11, 0.5);
}

/* Enhanced missing row styling */
.missing-row {
    background: linear-gradient(90deg, rgba(253, 224, 71, 0.15) 0%, rgba(253, 224, 71, 0.25) 50%, rgba(253, 224, 71, 0.15) 100%);
    border-left: 4px solid #fbbf24;
}

.missing-row td {
    color: #5c5c5c !important;
    font-weight: 500;
}

/* Enhanced cell mismatch highlighting */
.cell-mismatch {
    background: linear-gradient(135deg, rgba(253, 224, 71, 0.4) 0%, rgba(253, 224, 71, 0.5) 100%) !important;
    border: 1px solid #fbbf24 !important;
    color: #1f2937 !important;
    font-weight: 600 !important;
    box-shadow: 0 0 8px rgba(251, 191, 36, 0.3), inset 0 1px 2px rgba(251, 191, 36, 0.2);
    position: relative;
}

.cell-mismatch::before {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
    background: linear-gradient(45deg, transparent 30%, rgba(255, 255, 255, 0.1) 50%, transparent 70%);
    pointer-events: none;
}

/* Account mismatch row styling */
.account-mismatch-row {
    border-left: 4px solid #eab308 !important;
    background: rgba(234, 179, 8, 0.05);
}

/* Sticky header improvements */
#file1Headers, #file2Headers {
    position: sticky;
    top: 0;
    z-index: 10;
    backdrop-filter: blur(8px);
}

/* Scroll area improvements */
#file1ScrollArea, #file2ScrollArea {
    scrollbar-width: thin;
    scrollbar-color: rgba(59, 130, 246, 0.5) rgba(31, 41, 55, 0.3);
}

/* Row height consistency */
tbody tr {
    min-height: 32px;
}

tbody tr.gap-row {
    min-height: 40px;
}

/* Text alignment and spacing */
.side-by-side-comparison table {
    table-layout: fixed;
    width: 100%;
}

.side-by-side-comparison th,
.side-by-side-comparison td {
    text-align: left;
    vertical-align: middle;
}

/* Synchronized scrolling indicator */
.scroll-sync-active {
    border: 2px solid rgba(59, 130, 246, 0.3);
    box-shadow: 0 0 10px rgba(59, 130, 246, 0.2);
}

/* Improved hover effects for data rows */
tr:hover td {
    background: rgba(59, 130, 246, 0.05) !important;
    transition: background-color 0.15s ease;
}

/* Enhanced table borders */
.side-by-side-comparison table {
    border-collapse: separate;
    border-spacing: 0;
}

.side-by-side-comparison td,
.side-by-side-comparison th {
    border-right: 1px solid rgba(107, 114, 128, 0.2);
}

.side-by-side-comparison td:last-child,
.side-by-side-comparison th:last-child {
    border-right: none;
}

/* ======================================================================
 * Joint Side-by-Side View Styles
 * ====================================================================== */

/* Joint view column alternating colors */
.joint-view-file1-col {
    background: rgba(6, 182, 212, 0.05);
    border-right: 1px solid rgba(6, 182, 212, 0.2);
    font-family: 'Consolas', 'Monaco', 'Courier New', monospace;
}

.joint-view-file2-col {
    background: rgba(34, 197, 94, 0.05);
    border-right: 1px solid rgba(34, 197, 94, 0.2);
    font-family: 'Consolas', 'Monaco', 'Courier New', monospace;
}

/* Enhanced column headers for joint view */
.joint-view-header-file1 {
    background: linear-gradient(135deg, rgba(6, 182, 212, 0.2) 0%, rgba(6, 182, 212, 0.1) 100%);
    border-right: 2px solid rgba(6, 182, 212, 0.4);
}

.joint-view-header-file2 {
    background: linear-gradient(135deg, rgba(34, 197, 94, 0.2) 0%, rgba(34, 197, 94, 0.1) 100%);
}

/* Joint view missing data styling */
.joint-missing-file1 {
    background: transparent !important;
    color: #e2e8f0 !important;
}

.joint-missing-file2 {
    background: transparent !important;
    color: #e2e8f0 !important;
}

/* Enhanced account mismatch warning for joint view */
.joint-account-mismatch {
    background: linear-gradient(90deg, 
        rgba(245, 158, 11, 0.1) 0%, 
        rgba(245, 158, 11, 0.2) 25%, 
        rgba(245, 158, 11, 0.3) 50%, 
        rgba(245, 158, 11, 0.2) 75%, 
        rgba(245, 158, 11, 0.1) 100%);
    border: 2px solid rgba(245, 158, 11, 0.4);
    box-shadow: 0 0 15px rgba(245, 158, 11, 0.2);
}

/* Simple clean mismatch highlighting */
.joint-cell-mismatch-file1 {
    background: #fde047 !important;
    color: #1f2937 !important;
    font-weight: 500;
}

.joint-cell-mismatch-file2 {
    background: #fde047 !important;
    color: #1f2937 !important;
    font-weight: 500;
}

/* Simple "Missing" text styling */
.data-mismatch-indicator {
    color: #ffffff !important;
    font-weight: 500;
    font-size: 11px;
}

/* Intermediate Comparison Column Styles */
.intermediate-comparison-column {
    background: linear-gradient(135deg, rgba(139, 92, 246, 0.1), rgba(167, 139, 250, 0.05));
    border-left: 2px solid rgba(139, 92, 246, 0.3);
    border-right: 2px solid rgba(139, 92, 246, 0.3);
}

.intermediate-comparison-header {
    background: linear-gradient(135deg, rgba(139, 92, 246, 0.2), rgba(167, 139, 250, 0.1));
    border: 1px solid rgba(139, 92, 246, 0.4);
}

.intermediate-match {
    background: linear-gradient(135deg, rgba(34, 197, 94, 0.15), rgba(34, 197, 94, 0.05));
    color: #22c55e;
}

.intermediate-partial {
    background: linear-gradient(135deg, rgba(251, 191, 36, 0.15), rgba(251, 191, 36, 0.05));
    color: #fbbf24;
}

.intermediate-mismatch {
    background: linear-gradient(135deg, rgba(239, 68, 68, 0.15), rgba(239, 68, 68, 0.05));
    color: #ef4444;
}

.intermediate-missing {
    background: linear-gradient(135deg, rgba(156, 163, 175, 0.15), rgba(156, 163, 175, 0.05));
    color: #9ca3af;
}

/* Improved hover effects for joint view */
.joint-view tbody tr:hover .joint-view-file1-col {
    background: rgba(6, 182, 212, 0.15);
}

.joint-view tbody tr:hover .joint-view-file2-col {
    background: rgba(34, 197, 94, 0.15);
}

/* Professional table styling */
.comparison-table {
    border-spacing: 0;
    width: 100%;
    table-layout: fixed;
    max-width: 100%;
}

.comparison-table th,
.comparison-table td {
    border-right: 1px solid rgba(107, 114, 128, 0.2);
    text-align: left;
    vertical-align: middle;
    padding: 3px 6px;
}

.comparison-table th {
    overflow: visible;
    white-space: normal;
    word-wrap: break-word;
    line-height: 1.1;
}

.comparison-table td {
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

.comparison-table th:last-child,
.comparison-table td:last-child {
    border-right: none;
}

/* Account ID and Number specific styling */
.account-column {
    font-family: 'Consolas', 'Monaco', 'Courier New', monospace !important;
    font-size: 9px !important;
    font-weight: 600 !important;
    letter-spacing: 0.2px;
    width: 140px !important;
    max-width: 140px !important;
    overflow: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
}

/* Professional text rendering */
.professional-text {
    font-size: 10px;
    line-height: 1.3;
    font-weight: 500;
    color: #e2e8f0;
}

/* Better scrollbar for horizontal scrolling */
.comparison-container::-webkit-scrollbar {
    height: 8px;
}

.comparison-container::-webkit-scrollbar-track {
    background: rgba(31, 41, 55, 0.5);
    border-radius: 4px;
}

.comparison-container::-webkit-scrollbar-thumb {
    background: rgba(59, 130, 246, 0.5);
    border-radius: 4px;
}

.comparison-container::-webkit-scrollbar-thumb:hover {
    background: rgba(59, 130, 246, 0.7);
}

/* Responsive column widths */
.comparison-table th:nth-child(1),
.comparison-table td:nth-child(1) { width: 11%; } /* Account Number */
.comparison-table th:nth-child(2),
.comparison-table td:nth-child(2) { width: 11%; } /* Account Holder Name */
.comparison-table th:nth-child(3),
.comparison-table td:nth-child(3) { width: 9%; }  /* Last Update Date */
.comparison-table th:nth-child(4),
.comparison-table td:nth-child(4) { width: 8%; }  /* Status */
.comparison-table th:nth-child(5),
.comparison-table td:nth-child(5) { width: 9%; }  /* Balance */
.comparison-table th:nth-child(6),
.comparison-table td:nth-child(6) { width: 11%; } /* Account Holder Name */
.comparison-table th:nth-child(7),
.comparison-table td:nth-child(7) { width: 11%; } /* Account ID */
.comparison-table th:nth-child(8),
.comparison-table td:nth-child(8) { width: 9%; }  /* Balance */
.comparison-table th:nth-child(9),
.comparison-table td:nth-child(9) { width: 8%; }  /* Status */
.comparison-table th:nth-child(10),
.comparison-table td:nth-child(10) { width: 9%; } /* Date */

/* Compact text for better fit */
.comparison-table {
    font-size: 9px;
    line-height: 1.2;
}

/* Header specific styling */
.comparison-table th {
    height: auto;
    min-height: 40px;
    vertical-align: top;
}

.comparison-table th span {
    display: block;
    word-break: break-word;
    hyphens: auto;
}

/*
 * ======================================================================
 * Summary Button Styles - Glowing Interactive Cards
 * ======================================================================
 */

.summary-button-container {
    perspective: 1000px;
    transform-style: preserve-3d;
}

.summary-button {
    position: relative;
    padding: 1.5rem;
    border-radius: 16px;
    text-align: center;
    color: white;
    font-weight: 600;
    cursor: pointer;
    transform: translateZ(0);
    transition: all 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
    border: 1px solid rgba(255, 255, 255, 0.1);
    backdrop-filter: blur(10px);
    box-shadow: 
        0 8px 32px rgba(0, 0, 0, 0.4),
        0 0 0 1px rgba(255, 255, 255, 0.05),
        inset 0 1px 0 rgba(255, 255, 255, 0.1);
    
    /* Animated glow effect */
    animation: pulseGlow 3s ease-in-out infinite alternate;
}

.summary-button:hover {
    transform: translateY(-8px) scale(1.05) rotateX(5deg);
    box-shadow: 
        0 20px 40px rgba(0, 0, 0, 0.6),
        0 0 0 1px rgba(255, 255, 255, 0.1),
        inset 0 1px 0 rgba(255, 255, 255, 0.2);
    animation-play-state: paused;
}

.summary-button:active {
    transform: translateY(-4px) scale(1.02);
    transition: all 0.1s ease;
}

.summary-icon {
    font-size: 2.5rem;
    margin-bottom: 0.5rem;
    filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.3));
    animation: iconFloat 2s ease-in-out infinite alternate;
}

.summary-label {
    font-size: 0.875rem;
    opacity: 0.9;
    margin-bottom: 0.25rem;
    text-transform: uppercase;
    letter-spacing: 0.05em;
    font-weight: 500;
}

.summary-value {
    font-size: 1.875rem;
    font-weight: 800;
    text-shadow: 0 0 20px rgba(255, 255, 255, 0.5);
    animation: valueGlow 2s ease-in-out infinite alternate;
}

/* Pulse glow animation */
@keyframes pulseGlow {
    0% {
        box-shadow: 
            0 8px 32px rgba(0, 0, 0, 0.4),
            0 0 20px var(--shadow-color, rgba(0, 212, 255, 0.3)),
            0 0 0 1px rgba(255, 255, 255, 0.05),
            inset 0 1px 0 rgba(255, 255, 255, 0.1);
    }
    100% {
        box-shadow: 
            0 8px 32px rgba(0, 0, 0, 0.4),
            0 0 40px var(--shadow-color, rgba(0, 212, 255, 0.5)),
            0 0 0 1px rgba(255, 255, 255, 0.05),
            inset 0 1px 0 rgba(255, 255, 255, 0.1);
    }
}

/* Icon floating animation */
@keyframes iconFloat {
    0% {
        transform: translateY(0px) rotate(0deg);
        filter: drop-shadow(0 0 10px rgba(255, 255, 255, 0.3));
    }
    100% {
        transform: translateY(-4px) rotate(2deg);
        filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.5));
    }
}

/* Value glow animation */
@keyframes valueGlow {
    0% {
        text-shadow: 0 0 20px rgba(255, 255, 255, 0.5);
    }
    100% {
        text-shadow: 0 0 30px rgba(255, 255, 255, 0.8), 0 0 40px rgba(255, 255, 255, 0.3);
    }
}

/* Individual button shadow colors */
.summary-button.shadow-cyan-500\/50 {
    --shadow-color: rgba(6, 182, 212, 0.5);
}

.summary-button.shadow-green-500\/50 {
    --shadow-color: rgba(34, 197, 94, 0.5);
}

.summary-button.shadow-red-500\/50 {
    --shadow-color: rgba(239, 68, 68, 0.5);
}

.summary-button.shadow-yellow-500\/50 {
    --shadow-color: rgba(245, 158, 11, 0.5);
}

/* Enhanced hover effects for each button type */
.summary-button.bg-gradient-to-br.from-cyan-600:hover {
    background: linear-gradient(135deg, #0891b2, #1e40af, #3b82f6);
    --shadow-color: rgba(6, 182, 212, 0.7);
}

.summary-button.bg-gradient-to-br.from-green-600:hover {
    background: linear-gradient(135deg, #16a34a, #059669, #0d9488);
    --shadow-color: rgba(34, 197, 94, 0.7);
}

.summary-button.bg-gradient-to-br.from-red-600:hover {
    background: linear-gradient(135deg, #dc2626, #e11d48, #be185d);
    --shadow-color: rgba(239, 68, 68, 0.7);
}

.summary-button.bg-gradient-to-br.from-yellow-600:hover {
    background: linear-gradient(135deg, #d97706, #ea580c, #dc2626);
    --shadow-color: rgba(245, 158, 11, 0.7);
}

/* Responsive adjustments */
@media (max-width: 768px) {
    .summary-button {
        padding: 1rem;
    }
    
    .summary-icon {
        font-size: 2rem;
    }
    
    .summary-value {
        font-size: 1.5rem;
    }
}

/* Accessibility improvements */
@media (prefers-reduced-motion: reduce) {
    .summary-button,
    .summary-icon,
    .summary-value {
        animation: none;
    }
    
    .summary-button:hover {
        transform: none;
    }
}
</style>
</head>
<body class="bg-gray-900 min-h-screen" style="background: linear-gradient(135deg, #0f1419 0%, #1a202c 50%, #2d3748 100%);">
    <div class="container mx-auto px-6 py-8 max-w-6xl">
        <div class="text-center mb-8 animate-float-up">
            <h1 class="text-4xl font-bold glow-text-primary mb-4">Compare Two Files</h1>
            <p class="text-xl glow-text-secondary">Compare data across two systems with Schema and Data Mapping</p>
        </div>

        <div class="neo-container p-8 mb-8">
            <div class="flex flex-col lg:flex-row lg:items-center lg:justify-between mb-6 gap-4">
                <h2 class="text-3xl font-semibold glow-text-accent" data-text="📁 Upload Files">📁 Upload Files</h2>
                
                <!-- Compact Configuration Options -->
                <div class="flex flex-wrap items-center gap-6 text-sm">
                    <label class="flex items-center space-x-2 cursor-pointer group">
                        <input type="checkbox" id="ignoreSpaces" class="neo-checkbox scale-75">
                        <span class="text-gray-300 group-hover:text-cyan-300 transition-colors">Ignore spaces</span>
                    </label>
                    <label class="flex items-center space-x-2 cursor-pointer group">
                        <input type="checkbox" id="caseInsensitive" class="neo-checkbox scale-75">
                        <span class="text-gray-300 group-hover:text-cyan-300 transition-colors">Case-insensitive</span>
                    </label>
                </div>
            </div>
            

            
            
            <div class="grid grid-cols-1 lg:grid-cols-3 gap-8">
                
                <!-- Column 1: Schema Files (Schema File 1 + Schema File 2) -->
                <div class="space-y-6">
                    <div class="text-center">
                        <h3 class="text-xl font-semibold text-glow-cyan mb-4 pb-3 border-b-2 border-cyan-400/40">
                            📊 Schema Files
                        </h3>
                    </div>
                    
                    <!-- Schema File 1 -->
                    <div class="file-upload-container">
                        <div class="neo-upload" data-step="1">
                            <div class="text-cyan-400 text-3xl mb-3">📊</div>
                            <h3 class="font-semibold text-gray-200 text-lg mb-2">Schema File 1</h3>
                            <p class="text-sm text-gray-400 mb-3">(.xlsx format)</p>
                            <div class="file-status text-sm font-medium text-yellow-400" data-status="pending">Enter the Path</div>
                            <input type="file" class="hidden" accept=".xlsx" data-file="schema1">
                            
                            <!-- File path input option -->
                            <div class="mt-4 pt-4 border-t border-gray-600">
                                <label class="block text-xs text-gray-400 mb-2">Or enter file path:</label>
                                <div class="flex space-x-2">
                                    <input type="text" 
                                           class="flex-1 bg-gray-700 border border-gray-600 rounded px-3 py-2 text-gray-300 text-sm placeholder-gray-500 min-w-0"
                                           placeholder="File will appear here after selection"
                                           data-path-input="schema1">
                                    <button type="button" 
                                            class="px-4 py-2 bg-cyan-600 hover:bg-cyan-700 text-white text-sm rounded transition-colors whitespace-nowrap flex-shrink-0"
                                            data-path-load="schema1">
                                        Browse
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Schema File 2 -->
                    <div class="file-upload-container">
                        <div class="neo-upload" data-step="2">
                            <div class="text-cyan-400 text-3xl mb-3">📊</div>
                            <h3 class="font-semibold text-gray-200 text-lg mb-2">Schema File 2</h3>
                            <p class="text-sm text-gray-400 mb-3">(.xlsx format)</p>
                            <div class="file-status text-sm font-medium text-yellow-400" data-status="pending">Enter the Path</div>
                            <input type="file" class="hidden" accept=".xlsx" data-file="schema2">
                            
                            <!-- File path input option -->
                            <div class="mt-4 pt-4 border-t border-gray-600">
                                <label class="block text-xs text-gray-400 mb-2">Or enter file path:</label>
                                <div class="flex space-x-2">
                                    <input type="text" 
                                           class="flex-1 bg-gray-700 border border-gray-600 rounded px-3 py-2 text-gray-300 text-sm placeholder-gray-500 min-w-0"
                                           placeholder="File will appear here after selection"
                                           data-path-input="schema2">
                                    <button type="button" 
                                            class="px-4 py-2 bg-cyan-600 hover:bg-cyan-700 text-white text-sm rounded transition-colors whitespace-nowrap flex-shrink-0"
                                            data-path-load="schema2">
                                        Browse
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Column 2: Data Files (Data File 1 + Data File 2) -->
                <div class="space-y-6">
                    <div class="text-center">
                        <h3 class="text-xl font-semibold text-glow-green mb-4 pb-3 border-b-2 border-green-400/40">
                            📄 Data Files
                        </h3>
                    </div>

                    <!-- Data File 1 -->
                    <div class="file-upload-container">
                        <div class="neo-upload" data-step="3">
                            <div class="text-green-400 text-3xl mb-3">📄</div>
                            <h3 class="font-semibold text-gray-200 text-lg mb-2">Data File 1</h3>
                            <p class="text-sm text-gray-400 mb-3">(.csv/.txt format)</p>
                            <div class="file-status text-sm font-medium text-yellow-400" data-status="pending">Enter the Path</div>
                            <input type="file" class="hidden" accept=".csv,.txt" data-file="data1">
                            
                            <!-- File path input option -->
                            <div class="mt-4 pt-4 border-t border-gray-600">
                                <label class="block text-xs text-gray-400 mb-2">Or enter file path:</label>
                                <div class="flex space-x-2">
                                    <input type="text" 
                                           class="flex-1 bg-gray-700 border border-gray-600 rounded px-3 py-2 text-gray-300 text-sm placeholder-gray-500 min-w-0"
                                           placeholder="File will appear here after selection"
                                           data-path-input="data1">
                                    <button type="button" 
                                            class="px-4 py-2 bg-green-600 hover:bg-green-700 text-white text-sm rounded transition-colors whitespace-nowrap flex-shrink-0"
                                            data-path-load="data1">
                                        Browse
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Data File 2 -->
                    <div class="file-upload-container">
                        <div class="neo-upload" data-step="4">
                            <div class="text-green-400 text-3xl mb-3">📄</div>
                            <h3 class="font-semibold text-gray-200 text-lg mb-2">Data File 2</h3>
                            <p class="text-sm text-gray-400 mb-3">(.csv/.txt format)</p>
                            <div class="file-status text-sm font-medium text-yellow-400" data-status="pending">Enter the Path</div>
                            <input type="file" class="hidden" accept=".csv,.txt" data-file="data2">
                            
                            <!-- File path input option -->
                            <div class="mt-4 pt-4 border-t border-gray-600">
                                <label class="block text-xs text-gray-400 mb-2">Or enter file path:</label>
                                <div class="flex space-x-2">
                                    <input type="text" 
                                           class="flex-1 bg-gray-700 border border-gray-600 rounded px-3 py-2 text-gray-300 text-sm placeholder-gray-500 min-w-0"
                                           placeholder="File will appear here after selection"
                                           data-path-input="data2">
                                    <button type="button" 
                                            class="px-4 py-2 bg-green-600 hover:bg-green-700 text-white text-sm rounded transition-colors whitespace-nowrap flex-shrink-0"
                                            data-path-load="data2">
                                        Browse
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <!-- Column 3: Data Mapping Files + Action Panel -->
                <div class="space-y-6">
                    <div class="text-center">
                        <h3 class="text-xl font-semibold text-glow-purple mb-4 pb-3 border-b-2 border-purple-400/40">
                            🔗 Data Mapping & Action
                        </h3>
                    </div>

                    <!-- Data Mapping File -->
                    <div class="file-upload-container">
                        <div class="neo-upload" data-step="5">
                            <div class="text-purple-400 text-3xl mb-3">🔗</div>
                            <h3 class="font-semibold text-gray-200 text-lg mb-2">Data Mapping File</h3>
                            <p class="text-sm text-gray-400 mb-3">(.xlsx format)</p>
                            <div class="file-status text-sm font-medium text-yellow-400" data-status="pending">Enter the Path</div>
                            <input type="file" class="hidden" accept=".xlsx" data-file="mapping">
                            
                            <!-- File path input option -->
                            <div class="mt-4 pt-4 border-t border-gray-600">
                                <label class="block text-xs text-gray-400 mb-2">Or enter file path:</label>
                                <div class="flex space-x-2">
                                    <input type="text" 
                                           class="flex-1 bg-gray-700 border border-gray-600 rounded px-3 py-2 text-gray-300 text-sm placeholder-gray-500 min-w-0"
                                           placeholder="File will appear here after selection"
                                           data-path-input="mapping">
                                    <button type="button" 
                                            class="px-4 py-2 bg-purple-600 hover:bg-purple-700 text-white text-sm rounded transition-colors whitespace-nowrap flex-shrink-0"
                                            data-path-load="mapping">
                                        Browse
                                    </button>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Action Panel -->
                    <div class="file-upload-container">
                        <div class="neo-upload bg-gradient-to-br from-blue-900/30 to-indigo-900/30 border-blue-400/40" style="min-height: 200px; display: flex; flex-direction: column; justify-content: center; align-items: center;">
                            <div class="text-blue-400 text-3xl mb-3">🔍</div>
                            <button id="compareBtn" class="neo-button text-lg py-3 px-8 disabled:opacity-50 disabled:cursor-not-allowed" disabled>
                                🔍 Compare Files
                            </button>
                            <p class="text-sm text-gray-400 mt-3 text-center">Click to compare all uploaded files</p>
                        </div>
                    </div>
                </div>
            </div>
        </div>



        <div id="summarySection" class="neo-container p-8 mb-8 hidden">
            <h2 class="text-3xl font-semibold glow-text-accent mb-8 text-center" data-text="📊 Comparison Summary">📊 Comparison Summary</h2>
            
            <!-- Glowing Button Grid -->
            <div class="grid grid-cols-2 lg:grid-cols-4 gap-6 max-w-6xl mx-auto">
                
                <!-- Total Rows Button -->
                <div class="summary-button-container">
                    <div class="summary-button bg-gradient-to-br from-cyan-600 to-blue-700 hover:from-cyan-500 hover:to-blue-600 shadow-cyan-500/50">
                        <div class="summary-icon">📊</div>
                        <div class="summary-label">Total Rows</div>
                        <div class="summary-value" id="totalRows">0</div>
                    </div>
                </div>
                
                <!-- Matched Rows Button -->
                <div class="summary-button-container">
                    <div class="summary-button bg-gradient-to-br from-green-600 to-emerald-700 hover:from-green-500 hover:to-emerald-600 shadow-green-500/50">
                        <div class="summary-icon">✅</div>
                        <div class="summary-label">Matched</div>
                        <div class="summary-value" id="matchedRows">0</div>
                    </div>
                </div>
                
                <!-- Mismatched Rows Button -->
                <div class="summary-button-container">
                    <div class="summary-button bg-gradient-to-br from-red-600 to-rose-700 hover:from-red-500 hover:to-rose-600 shadow-red-500/50">
                        <div class="summary-icon">❌</div>
                        <div class="summary-label">Mismatched</div>
                        <div class="summary-value" id="mismatchedRows">0</div>
                    </div>
                </div>
                
                <!-- Accuracy Button -->
                <div class="summary-button-container">
                    <div class="summary-button bg-gradient-to-br from-yellow-600 to-orange-700 hover:from-yellow-500 hover:to-orange-600 shadow-yellow-500/50">
                        <div class="summary-icon">🎯</div>
                        <div class="summary-label">Accuracy</div>
                        <div class="summary-value" id="accuracyPercentage">0%</div>
                    </div>
                </div>
                
            </div>
        </div>

        <div id="swappedRowsSection" class="neo-container p-8 mb-8 hidden">
            <h2 class="text-3xl font-semibold glow-text-accent mb-6" data-text="🔄 Swapped Rows">🔄 Swapped Rows</h2>
            <p class="text-gray-300 text-lg mb-6">Rows that appear in different positions between the two files:</p>
            
            <div class="overflow-x-auto rounded-xl border border-gray-600">
                <table class="neo-table min-w-full">
                    <thead>
                        <tr>
                            <th class="neo-table th">Key Value</th>
                            <th class="neo-table th">File 1 Row</th>
                            <th class="neo-table th">File 2 Row</th>
                            <th class="neo-table th">Status</th>
                        </tr>
                    </thead>
                    <tbody id="swappedRowsTableBody">
                        </tbody>
                </table>
            </div>
        </div>

        <div id="resultsSection" class="neo-container p-6 mb-6 hidden">
            <h2 class="text-2xl font-semibold glow-text-accent mb-4" data-text="📊 All Data Files">📊 All Data Files</h2>
            <p class="text-gray-300 text-sm mb-4">Complete data from both files in a single view:</p>
            
            <div id="successMessage" class="bg-gradient-to-r from-green-500 to-emerald-600 text-white p-8 rounded-2xl text-center hidden shadow-2xl border border-green-400/30" style="box-shadow: 0 0 30px rgba(16, 185, 129, 0.4), 0 0 60px rgba(16, 185, 129, 0.2);">
                <div class="text-5xl mb-4">🎉</div>
                <div class="text-2xl font-bold mb-2">All rows match perfectly!</div>
                <div class="text-lg opacity-90">No discrepancies found in the data comparison.</div>
            </div>

            <!-- Column Selection for Intermediate Comparison -->
            <div id="columnSelectionPanel" class="neo-container p-6 mb-8 hidden">
                <h2 class="text-2xl font-semibold glow-text-accent mb-4" data-text="🔍 Intermediate Comparison Setup">🔍 Intermediate Comparison Setup</h2>
                <div class="mb-4">
                    <p class="text-gray-300 mb-4">Select specific columns from your mapping sheet to focus your comparison analysis:</p>
                    <div id="availableColumnsContainer" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4 mb-6">
                        <!-- Column checkboxes will be populated by JavaScript -->
                    </div>
                    <div class="flex gap-4">
                        <button id="selectAllColumnsBtn" class="neo-button bg-blue-600 hover:bg-blue-700">
                            ✅ Select All
                        </button>
                        <button id="clearColumnsBtn" class="neo-button bg-gray-600 hover:bg-gray-700">
                            ❌ Clear All
                        </button>
                    </div>
                </div>
            </div>

            <!-- Side-by-side comparison layout -->
            <div id="sideBySideComparison" class="hidden">
                <!-- Simple header -->
                <div class="mb-4 text-center">
                    <span class="text-xl font-semibold text-cyan-300">📊 File Comparison Results</span>
                </div>
                
                <!-- Download Button for Comparison Results -->
                <div class="text-center mb-4">
                    <button id="downloadComparisonBtn" class="neo-button text-sm py-2 px-4" style="background: linear-gradient(135deg, #8b5cf6 0%, #a855f7 100%); box-shadow: 0 0 15px rgba(139, 92, 246, 0.3);">
                        💾 Download Results (Excel)
                    </button>
                </div>

                <!-- Main comparison grid -->
                <div class="grid grid-cols-1 gap-4">

                    <!-- File 1 Column (Full Width) -->
                    <div class="bg-gradient-to-br from-cyan-900/20 to-blue-900/20 rounded-lg border border-cyan-400/40 overflow-hidden">
                        <!-- File 1 Header (Sticky) -->
                        <div class="sticky top-0 z-20 bg-gradient-to-r from-cyan-600/90 to-blue-600/90 backdrop-blur-sm">
                            <div class="px-3 py-1 border-b border-cyan-400/30">
                                <div class="flex items-center justify-between">
                                    <div class="flex items-center space-x-2">
                                        <span class="text-lg">📊</span>
                                        <div>
                                            <h4 class="text-sm font-semibold text-cyan-300">Joint File Comparison</h4>
                                            <p class="text-xs text-cyan-200/80" id="file1Stats">Loading...</p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!-- Sticky column headers -->
                            <div class="overflow-x-hidden">
                                <div id="file1Headers" class="w-full bg-cyan-800/40">
                                    <!-- Headers will be populated by JavaScript -->
                                </div>
                            </div>
                        </div>
                        
                        <!-- File 1 Data (Scrollable) -->
                        <div id="file1ScrollArea" class="comparison-container overflow-x-hidden overflow-y-auto max-h-screen bg-gray-900/50" style="max-height: calc(100vh - 280px);">
                            <div id="file1Data" class="w-full">
                                <!-- Data rows will be populated by JavaScript -->
                            </div>
                        </div>
                    </div>

                    <!-- File 2 Column (Hidden) -->
                    <div id="file2Panel" class="bg-gradient-to-br from-green-900/20 to-emerald-900/20 rounded-lg border border-green-400/40 overflow-hidden hidden">
                        <!-- File 2 Header (Sticky) -->
                        <div class="sticky top-0 z-20 bg-gradient-to-r from-green-600/90 to-emerald-600/90 backdrop-blur-sm">
                            <div class="px-4 py-3 border-b border-green-400/30">
                                <div class="flex items-center justify-between">
                                    <div class="flex items-center space-x-2">
                                        <span class="text-2xl">📄</span>
                                        <div>
                                            <h4 class="text-lg font-semibold text-green-300">File 2</h4>
                                            <p class="text-xs text-green-200/80" id="file2Stats">Loading...</p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                            <!-- Sticky column headers -->
                            <div class="overflow-x-auto">
                                <div id="file2Headers" class="min-w-full bg-green-800/30">
                                    <!-- Headers will be populated by JavaScript -->
                                </div>
                            </div>
                        </div>
                        
                        <!-- File 2 Data (Scrollable) -->
                        <div id="file2ScrollArea" class="overflow-x-auto overflow-y-auto max-h-96 bg-gray-900/50">
                            <div id="file2Data" class="min-w-full">
                                <!-- Data rows will be populated by JavaScript -->
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div id="resultsTable">
                <!-- This will be dynamically populated with the old table format as fallback -->
            </div>
        </div>

        <div id="downloadSection" class="neo-container p-8 mb-8 hidden">
            <h2 class="text-3xl font-semibold glow-text-accent mb-6" data-text="💾 Download Results">💾 Download Results</h2>
            <div class="flex flex-col sm:flex-row gap-6">
                <button id="downloadMismatchedBtn" class="neo-button text-lg py-4 px-8" style="background: linear-gradient(135deg, #ef4444 0%, #ec4899 100%);">
                    📊 Download Comparison Results (Excel)
                </button>
                <button id="downloadComparedBtn" class="neo-button text-lg py-4 px-8" style="background: linear-gradient(135deg, #10b981 0%, #059669 100%);">
                    📄 Download Compared File (CSV)
                </button>
            </div>
        </div>

        <div id="intermediateSummarySection" class="neo-container p-8 mb-8 hidden">
            <h2 class="text-3xl font-semibold glow-text-accent mb-6" data-text="📊 Intermediate Comparison Summary">📊 Intermediate Comparison Summary</h2>
            <div class="grid grid-cols-2 md:grid-cols-4 gap-6">
                <div class="neo-stat text-glow-cyan">
                    <div class="neo-stat-number text-blue-400" id="intermediateTotalRows">0</div>
                    <div class="neo-stat-label">Total Compared</div>
                </div>
                <div class="neo-stat text-glow-green">
                    <div class="neo-stat-number text-green-400" id="intermediateMatchedRows">0</div>
                    <div class="neo-stat-label">Matching Rows</div>
                </div>
                <div class="neo-stat text-glow-red">
                    <div class="neo-stat-number text-red-400" id="intermediateMismatchedRows">0</div>
                    <div class="neo-stat-label">Differences Found</div>
                </div>
                <div class="neo-stat text-glow-purple">
                    <div class="neo-stat-number text-purple-400" id="intermediateMatchPercentage">0%</div>
                    <div class="neo-stat-label">Match Accuracy</div>
                </div>
            </div>
        </div>

        <div id="intermediateResultsSection" class="neo-container p-6 mb-6 hidden">
            <h2 class="text-2xl font-semibold glow-text-accent mb-4" data-text="🔄 All Intermediate Data">🔄 All Intermediate Data</h2>
            <p class="text-gray-300 text-sm mb-4">Complete data from both intermediate files in a single view:</p>
            
            <div id="intermediateSuccessMessage" class="bg-gradient-to-r from-green-500 to-emerald-600 text-white p-8 rounded-2xl text-center hidden shadow-2xl border border-green-400/30" style="box-shadow: 0 0 30px rgba(16, 185, 129, 0.4), 0 0 60px rgba(16, 185, 129, 0.2);">
                <div class="text-5xl mb-4">🎉</div>
                <div class="text-2xl font-bold mb-2">All intermediate files match perfectly!</div>
                <div class="text-lg opacity-90">No discrepancies found in the intermediate comparison.</div>
            </div>

            <div id="intermediateResultsTable">
                <!-- This will be dynamically populated with the intermediate data tables -->
            </div>
        </div>

        <div id="intermediateDownloadSection" class="neo-container p-8 mb-8 hidden">
            <h2 class="text-3xl font-semibold glow-text-accent mb-6" data-text="💾 Download Intermediate Results">💾 Download Intermediate Results</h2>
            <div class="flex flex-col sm:flex-row gap-6">
                <button id="downloadIntermediateBtn" class="neo-button text-lg py-4 px-8" style="background: linear-gradient(135deg, #7c3aed 0%, #6366f1 100%);">
                    📊 Download Intermediate Comparison (Excel)
                </button>
                <button id="downloadIntermediateFilesBtn" class="neo-button text-lg py-4 px-8" style="background: linear-gradient(135deg, #6366f1 0%, #3b82f6 100%);">
                    📄 Download Intermediate Files (Excel)
                </button>
            </div>
        </div>

        <div id="loadingOverlay" class="fixed inset-0 bg-black/80 backdrop-blur-md flex items-center justify-center z-50 hidden">
            <div class="neo-container p-12 text-center max-w-md">
                <div class="neo-loader mx-auto mb-8"></div>
                <div class="text-2xl font-bold glow-text-primary mb-4">Processing files...</div>
                <div class="text-lg text-gray-400">This may take a few moments for large files</div>
            </div>
        </div>
    </div>
    <script>
// Global variables to store uploaded files and comparison results
let uploadedFiles = {
    schema1: null,
    schema2: null,
    data1: null,
    data2: null,
    mapping: null
};

let comparisonResults = null;
let intermediateResults = {
    allData: [],
    totalRows: 0,
    matchedRows: 0,
    mismatchedRows: 0,
    columnMappings: {}
};

// Store parsed data content globally for display
let parsedDataContent = {
    data1: null,
    data2: null,
    intermediate1: null,
    intermediate2: null,
    mappingData: null,
    columnMappings: null
};

// Global variables for column selection
let availableColumns = {
    file1: [],
    file2: []
};

let selectedColumns = {
    file1: [],
    file2: []
};

let columnMappings = [];

// Global variable to store available column mappings for selection
let availableColumnMappings = null;

// Global variables for intermediate comparison
let selectedIntermediateColumns = new Set();
let intermediateComparisonActive = false;
let intermediateComparisonCounter = 0; // Counter for multiple intermediate comparisons

// File upload step management
let currentStep = 1;
const totalSteps = 5;

// Side-by-side comparison variables
let showAllRowsMode = false;
let scrollSyncEnabled = true;
let sideBySideData = {
    file1Rows: [],
    file2Rows: [],
    rowMappings: [], // Maps which rows correspond to each other
    accountIdColumn: null,
    accountNumberColumn: null
};

// Initialize the application
document.addEventListener('DOMContentLoaded', function() {
    console.log('DOM loaded, initializing...');
    
    // Update debug status
    updateDebugStatus('jsStatus', '✅ Loaded', 'text-green-400');
    
    // Add simple test to verify DOM elements exist
    const uploadAreas = document.querySelectorAll('.neo-upload');
    console.log('Found upload areas:', uploadAreas.length);
    updateDebugStatus('areasStatus', `✅ Found ${uploadAreas.length}`, 'text-green-400');
    
    uploadAreas.forEach((area, index) => {
        const step = area.getAttribute('data-step');
        const fileInput = area.querySelector('input[type="file"]');
        const browseButton = area.querySelector('[data-path-load]');
        console.log(`Step ${step} - File input found:`, !!fileInput, 'Browse button found:', !!browseButton);
    });
    
    // Simple direct approach to fix browse buttons
    console.log('🔧 SETTING UP BROWSE BUTTONS DIRECTLY...');
    setupBrowseButtonsDirectly();
    
    initializeFileUploads();
    initializeEventListeners();
    initializeIntermediateComparison();
    
    // Enable all steps
    for (let step = 1; step <= 5; step++) {
        enableStep(step);
    }
    updateDebugStatus('currentStepStatus', 'All enabled', 'text-green-400');
    
    // Test click functionality
    console.log('Initialization complete. Testing upload areas...');
    
    // Update files status
    updateFilesStatus();
});

// Initialize intermediate comparison functionality
function initializeIntermediateComparison() {
    console.log('🔧 Initializing intermediate comparison...');
    
    const selectAllBtn = document.getElementById('selectAllColumnsBtn');
    const clearBtn = document.getElementById('clearColumnsBtn');
    const resetBtn = document.getElementById('resetIntermediateBtn');
    
    console.log('Buttons found:', {
        selectAll: !!selectAllBtn,
        clear: !!clearBtn,
        reset: !!resetBtn
    });
    
    if (selectAllBtn) {
        selectAllBtn.addEventListener('click', selectAllIntermediateColumns);
    }
    
    if (clearBtn) {
        clearBtn.addEventListener('click', clearAllIntermediateColumns);
    }
    
    if (resetBtn) {
        resetBtn.addEventListener('click', resetIntermediateComparison);
    }
    
    console.log('✅ Intermediate comparison initialized');
    
    // Add global test function for debugging
    window.testIntermediatePanel = function() {
        console.log('🧪 Testing intermediate panel...');
        console.log('Current parsedDataContent:', parsedDataContent);
        
        // Create test mapping if none exists
        if (!parsedDataContent.mappingArray) {
            parsedDataContent.mappingArray = [
                { file1Column: 'Account ID', file2Column: 'Account Number' },
                { file1Column: 'Account Holder Name', file2Column: 'Account Holder Name' },
                { file1Column: 'Closing Balance', file2Column: 'Closing Balance' }
            ];
            console.log('Created test mapping array');
        }
        
        // Create test data if none exists
        if (!parsedDataContent.data1) {
            parsedDataContent.data1 = [
                ['Account ID', 'Account Holder Name', 'Closing Balance'],
                ['1001', 'John Smith', '150.00'],
                ['1002', 'Sarah Wilson', '275.50']
            ];
            console.log('Created test data1');
        }
        
        if (!parsedDataContent.data2) {
            parsedDataContent.data2 = [
                ['Account Number', 'Account Holder Name', 'Closing Balance'],
                ['ACC1001', 'John Smith', '150.00'],
                ['ACC1002', 'Sarah Wilson', '275.50']
            ];
            console.log('Created test data2');
        }
        
        // Show column selection
        showIntermediateColumnSelection();
    };
    
    console.log('💡 Run window.testIntermediatePanel() in console to test');
    
    // Add global test function for file uploads
    window.testFileUploads = function() {
        console.log('🧪 Testing file upload functionality...');
        
        // Check if upload areas exist
        const uploadAreas = document.querySelectorAll('.neo-upload');
        console.log('Upload areas found:', uploadAreas.length);
        
        uploadAreas.forEach((area, index) => {
            const step = area.getAttribute('data-step');
            const fileInput = area.querySelector('input[type="file"]');
            const browseButton = area.querySelector('[data-path-load]');
            const pathInput = area.querySelector('[data-path-input]');
            
            console.log(`Area ${step}:`, {
                area: !!area,
                fileInput: !!fileInput,
                browseButton: !!browseButton,
                pathInput: !!pathInput,
                disabled: area.classList.contains('disabled')
            });
            
            if (browseButton) {
                console.log(`Testing browse button for ${step}...`);
                // Test if browse button responds
                browseButton.style.backgroundColor = '#ff0000';
                setTimeout(() => {
                    browseButton.style.backgroundColor = '';
                }, 1000);
            }
        });
        
        // Check if browse buttons work
        const browseButtons = document.querySelectorAll('[data-path-load]');
        console.log('Browse buttons found:', browseButtons.length);
        
        browseButtons.forEach((btn, index) => {
            const fileKey = btn.getAttribute('data-path-load');
            console.log(`Browse button ${index + 1} - fileKey: ${fileKey}, enabled: ${!btn.disabled}`);
        });
    };
    
    console.log('💡 Run window.testFileUploads() in console to test file upload functionality');
    
    // Add function to create and upload test files automatically
    window.createTestFiles = function() {
        console.log('🧪 Creating and uploading test files...');
        
        try {
            // Use existing test file generation
            generateTestFiles();
            
            console.log('✅ Test files creation initiated');
        } catch (error) {
            console.error('❌ Error creating test files:', error);
        }
    };
    
    console.log('💡 Run window.createTestFiles() in console to automatically create and upload test files');
    
    // Add a simple function to check if basic elements are accessible
    window.checkBasics = function() {
        console.log('🔍 Checking basic functionality...');
        
        // Check if basic elements exist
        const body = document.body;
        const uploadAreas = document.querySelectorAll('.neo-upload');
        const browseButtons = document.querySelectorAll('[data-path-load]');
        const fileInputs = document.querySelectorAll('input[type="file"]');
        
        console.log('Basic checks:', {
            bodyExists: !!body,
            uploadAreasCount: uploadAreas.length,
            browseButtonsCount: browseButtons.length,
            fileInputsCount: fileInputs.length
        });
        
        // Try to trigger a simple file input
        if (fileInputs.length > 0) {
            console.log('Testing first file input...');
            try {
                fileInputs[0].click();
                console.log('✅ File input click worked');
            } catch (error) {
                console.error('❌ File input click failed:', error);
            }
        }
        
        // Check if browse buttons are responsive
        browseButtons.forEach((btn, index) => {
            try {
                btn.style.backgroundColor = 'red';
                setTimeout(() => btn.style.backgroundColor = '', 500);
                console.log(`✅ Browse button ${index + 1} is responsive`);
            } catch (error) {
                console.error(`❌ Browse button ${index + 1} error:`, error);
            }
        });
    };
    
    console.log('💡 Run window.checkBasics() in console to test basic functionality');
    
    // Add global function to remove individual intermediate tables
    window.removeIntermediateTable = function(tableId) {
        const tableElement = document.getElementById(`intermediateTable_${tableId}`);
        if (tableElement) {
            tableElement.remove();
            console.log(`Removed intermediate table #${tableId}`);
        }
    };
    
    // Add global function to clear all intermediate tables
    window.clearAllIntermediateTables = function() {
        const dataContainer = document.getElementById('intermediateData');
        if (dataContainer) {
            dataContainer.innerHTML = '<div class="p-4 text-center text-purple-400">Select columns to see filtered results in real-time</div>';
            intermediateComparisonCounter = 0;
            console.log('Cleared all intermediate tables');
        }
    };
}

// Show column selection panel after mapping file is loaded
function showIntermediateColumnSelection() {
    console.log('🔍 showIntermediateColumnSelection called');
    console.log('parsedDataContent.mappingArray:', parsedDataContent.mappingArray);
    
    if (!parsedDataContent.mappingArray) {
        console.log('❌ No column mappings available for intermediate comparison');
        return;
    }
    
    const panel = document.getElementById('columnSelectionPanel');
    const container = document.getElementById('availableColumnsContainer');
    
    if (!panel || !container) return;
    
    // Clear existing content
    container.innerHTML = '';
    
    // Get available column mappings (extracted from your code)
    const mappingArray = parsedDataContent.mappingArray;
    
    if (mappingArray && mappingArray.length > 0) {
        mappingArray.forEach((mapping, index) => {
            const checkboxContainer = document.createElement('div');
            checkboxContainer.className = 'flex items-center space-x-2 p-3 bg-gray-800/50 rounded-lg border border-gray-600/50 hover:border-cyan-400/50 transition-all';
            
            const checkbox = document.createElement('input');
            checkbox.type = 'checkbox';
            checkbox.id = `intermediateCol_${index}`;
            checkbox.className = 'neo-checkbox';
            checkbox.value = `${mapping.file1Column}|${mapping.file2Column}`;
            checkbox.dataset.file1Column = mapping.file1Column;
            checkbox.dataset.file2Column = mapping.file2Column;
            
            // Add real-time event listener for instant updates
            checkbox.addEventListener('change', function() {
                console.log(`📋 Checkbox changed: ${mapping.file1Column} ↔ ${mapping.file2Column} - ${this.checked ? 'checked' : 'unchecked'}`);
                // Allow multi-select; just apply the filter
                applyIntermediateComparison();
            });
            
            const label = document.createElement('label');
            label.htmlFor = `intermediateCol_${index}`;
            label.className = 'text-sm text-gray-300 cursor-pointer flex-1';
            label.textContent = `${mapping.file1Column} ↔ ${mapping.file2Column}`;
            
            checkboxContainer.appendChild(checkbox);
            checkboxContainer.appendChild(label);
            container.appendChild(checkboxContainer);
        });
        
        panel.classList.remove('hidden');
    }
}

// Helper: normalize header text for matching
function normalizeHeaderName(name) {
    return (name || '').toString().toLowerCase().replace(/\s+/g, '').trim();
}

// Automatically select the requested pairs and render the intermediate table
function autoSelectPreferredIntermediateColumns() {
    try {
        const desiredPairs = [
            { file1: 'Client Name', file2: 'Account holder Name' },
            { file1: 'Client ID', file2: 'Account ID' },
            { file1: 'Transaction history', file2: 'Last Transaction Status' }
        ];
        const mappingArray = parsedDataContent.mappingArray || [];
        if (!mappingArray.length) return;
        
        // Ensure the checkboxes exist
        showIntermediateColumnSelection();
        
        const selectedMappings = [];
        
        desiredPairs.forEach(pair => {
            const file1Norm = normalizeHeaderName(pair.file1);
            const file2Norm = normalizeHeaderName(pair.file2);
            
            const idx = mappingArray.findIndex(m => 
                normalizeHeaderName(m.file1Column) === file1Norm && 
                normalizeHeaderName(m.file2Column) === file2Norm
            );
            
            if (idx >= 0) {
                const cb = document.getElementById(`intermediateCol_${idx}`);
                if (cb) {
                    cb.checked = true;
                }
                selectedMappings.push({ file1Column: mappingArray[idx].file1Column, file2Column: mappingArray[idx].file2Column });
            }
        });
        
        if (selectedMappings.length > 0) {
            // Apply to joint view
            window.selectedColumnFilter = selectedMappings;
            intermediateComparisonActive = true;
            renderSideBySideHeaders();
            renderJointSideBySideData();
            
            // Also render intermediate panel
            performIntermediateComparison(selectedMappings);
        }
    } catch (e) {
        console.warn('Auto-select preferred intermediate columns failed', e);
    }
}

// Select all intermediate columns
function selectAllIntermediateColumns() {
    const checkboxes = document.querySelectorAll('#availableColumnsContainer input[type="checkbox"]');
    checkboxes.forEach(checkbox => {
        checkbox.checked = true;
        selectedIntermediateColumns.add(checkbox.value);
    });
    // Trigger immediate update
    applyIntermediateComparison();
}

// Clear all intermediate column selections
function clearAllIntermediateColumns() {
    const checkboxes = document.querySelectorAll('#availableColumnsContainer input[type="checkbox"]');
    checkboxes.forEach(checkbox => {
        checkbox.checked = false;
    });
    selectedIntermediateColumns.clear();
    // Trigger immediate update (will hide panel since no columns selected)
    applyIntermediateComparison();
}

// Apply intermediate comparison with selected columns (extracted logic from your code)
function applyIntermediateComparison() {
    console.log('🔄 Starting column filtering for Joint File Comparison...');
    
    // Get selected columns (multi-select supported)
    selectedIntermediateColumns.clear();
    const selectedMappings = [];
    const checkboxes = document.querySelectorAll('#availableColumnsContainer input[type="checkbox"]:checked');
    
    console.log(`📋 Found ${checkboxes.length} checked checkboxes`);
    
    // Collect all checked mappings
    checkboxes.forEach((checkbox, index) => {
        const file1Column = checkbox.dataset.file1Column;
        const file2Column = checkbox.dataset.file2Column;
        console.log(`✅ Using mapping ${index + 1}: ${file1Column} ↔ ${file2Column}`);
        selectedIntermediateColumns.add(checkbox.value);
        selectedMappings.push({ file1Column, file2Column });
    });
    
    // Set global filter for Joint File Comparison
    window.selectedColumnFilter = selectedMappings;
    
    if (selectedMappings.length === 0) {
        // Show all columns when nothing selected
        console.log('📊 Showing all columns in Joint File Comparison');
        window.selectedColumnFilter = null;
        intermediateComparisonActive = false;
    } else {
        // Filter to selected pairs (multi-select)
        console.log('🔍 Filtering Joint File Comparison to selected pairs:', selectedMappings);
        intermediateComparisonActive = true;
    }
    
    // Re-render Joint File Comparison with current filter
    if (comparisonResults) {
        renderSideBySideHeaders();
        renderJointSideBySideData();
        console.log('✅ Joint File Comparison updated with selected pairs');
    }
}

// Perform intermediate comparison with selected mappings (extracted from your code)
function performIntermediateComparison(selectedMappings) {
    console.log('🔍 Performing intermediate comparison with selected mappings:', selectedMappings);
    
    if (!parsedDataContent.data1 || !parsedDataContent.data2) {
        showError('Data files are not loaded. Please upload data files first.');
        return;
    }
    
    try {
        // Get data content
        const data1Content = parsedDataContent.data1;
        const data2Content = parsedDataContent.data2;
        
        if (!data1Content || !data2Content) {
            showError('Data content is not available for intermediate comparison.');
            return;
        }
        
        // IMPORTANT: Make sure we have the latest comparison results and sideBySideData
        // This ensures sideBySideData is properly prepared for each intermediate comparison
        if (!comparisonResults || !sideBySideData.file1Rows || !sideBySideData.file2Rows) {
            console.log('🔄 Preparing side-by-side data for intermediate comparison...');
            // Get the latest comparison results
            const latestResults = window.comparisonResults || comparisonResults;
            if (latestResults) {
                prepareSideBySideData(latestResults);
                console.log('✅ Side-by-side data prepared for intermediate comparison');
            } else {
                showError('Comparison results not available. Please run the main comparison first.');
                return;
            }
        }
        
        // Create filtered data based on selected columns
        const filteredData1 = filterDataBySelectedColumns(data1Content, selectedMappings, 'file1');
        const filteredData2 = filterDataBySelectedColumns(data2Content, selectedMappings, 'file2');
        
        // Show intermediate file panel with filtered data
        showIntermediateFilePanel(filteredData1, filteredData2, selectedMappings);
        
    } catch (error) {
        console.error('Error in intermediate comparison:', error);
        showError('Error performing intermediate comparison: ' + error.message);
    }
}

// Filter data to show only selected columns
function filterDataBySelectedColumns(dataContent, selectedMappings, fileType) {
    if (!dataContent || dataContent.length === 0) return [];
    
    const headers = dataContent[0];
    const dataRows = dataContent.slice(1);
    
    // Find indices of selected columns
    const selectedIndices = [];
    const filteredHeaders = [];
    
    selectedMappings.forEach(mapping => {
        const columnName = fileType === 'file1' ? mapping.file1Column : mapping.file2Column;
        const columnIndex = headers.findIndex(header => 
            header.toLowerCase().trim() === columnName.toLowerCase().trim()
        );
        
        if (columnIndex >= 0) {
            selectedIndices.push(columnIndex);
            filteredHeaders.push(headers[columnIndex]);
        }
    });
    
    // Create filtered data with only selected columns
    const filteredRows = dataRows.map(row => 
        selectedIndices.map(index => row[index] || '')
    );
    
    return [filteredHeaders, ...filteredRows];
}

// Reset intermediate comparison
function resetIntermediateComparison() {
    selectedIntermediateColumns.clear();
    intermediateComparisonActive = false;
    
    // Hide intermediate file panel (main comparison stays visible)
    const intermediatePanel = document.getElementById('intermediateFilePanel');
    if (intermediatePanel) {
        intermediatePanel.classList.add('hidden');
    }
    
    // Clear the intermediate table content
    const intermediateData = document.getElementById('intermediateData');
    if (intermediateData) {
        intermediateData.innerHTML = '<div class="p-4 text-center text-purple-400">Select columns to see filtered results in real-time</div>';
    }
    
    // Column selection panel should already be visible, just ensure it's not hidden
    const selectionPanel = document.getElementById('columnSelectionPanel');
    if (selectionPanel) {
        selectionPanel.classList.remove('hidden');
    }
    
    // No need to restore header since we keep both tables visible
    
    // Re-render comparison without intermediate column
    if (comparisonResults) {
        displaySideBySideComparison(comparisonResults);
    }
}

function hideIntermediateFilePanel() {
    console.log('🔄 Hiding intermediate file panel...');
    
    const intermediatePanel = document.getElementById('intermediateFilePanel');
    if (intermediatePanel) {
        intermediatePanel.classList.add('hidden');
        console.log('✅ Intermediate panel hidden');
    }
    
    // Reset the comparison state
    intermediateComparisonActive = false;
    console.log('✅ Intermediate comparison state reset');
}

// Show intermediate file panel with filtered data
function showIntermediateFilePanel(filteredData1, filteredData2, selectedMappings) {
    console.log('🔍 Showing intermediate file panel with filtered data...');
    
    // Ensure DOM is ready
    if (!document.body) {
        console.error('DOM not ready, deferring intermediate panel display');
        setTimeout(() => showIntermediateFilePanel(filteredData1, filteredData2, selectedMappings), 100);
        return;
    }
    
    const intermediatePanel = document.getElementById('intermediateFilePanel');
    // Find the main comparison panel (the one right after intermediate panel)
    const mainPanel = intermediatePanel ? intermediatePanel.nextElementSibling : null;
    
    console.log('Intermediate panel found:', !!intermediatePanel);
    console.log('Main panel found:', !!mainPanel);
    console.log('Filtered Data1:', filteredData1);
    console.log('Filtered Data2:', filteredData2);
    
    if (!intermediatePanel) {
        console.error('Intermediate file panel not found!');
        return;
    }
    
    // Ensure the side-by-side comparison container is visible first
    const comparisonContainer = document.getElementById('sideBySideComparison');
    if (comparisonContainer) {
        comparisonContainer.classList.remove('hidden');
        console.log('Side-by-side container made visible');
    }
    
    // Show intermediate panel as a separate table (don't hide main comparison)
    intermediatePanel.classList.remove('hidden');
    console.log('Intermediate panel shown as separate table');
    
    // Keep main comparison visible - don't hide it
    // Users can see both tables: main comparison + intermediate filtered results
    
    // Generate intermediate file data with filtered content
    renderIntermediateFileHeaders(filteredData1, filteredData2, selectedMappings);
    renderIntermediateFileData(filteredData1, filteredData2, selectedMappings);
    
    // Update stats
    updateIntermediateStats();
    
    // Keep the original header unchanged since we're showing both tables
    // The intermediate panel has its own header in the HTML
    
    console.log('✅ Intermediate file panel setup complete');
}

// Render headers for intermediate file (only selected columns)
function renderIntermediateFileHeaders(filteredData1, filteredData2, selectedMappings) {
    console.log('📊 Rendering intermediate file headers...');
    
    const headersContainer = document.getElementById('intermediateHeaders');
    if (!headersContainer) {
        console.error('Headers container not found!');
        return;
    }
    
    if (!filteredData1 || !filteredData2 || filteredData1.length === 0 || filteredData2.length === 0) {
        console.error('Filtered data is not available for headers');
        return;
    }
    
    // Build exactly two headers for pairwise view
    let headerHTML = '<table class="comparison-table"><thead><tr>';
    headerHTML += `
        <th class="px-1 py-1 text-left text-xs font-semibold text-purple-200 uppercase tracking-tight border-b border-purple-400/50">
            <div class="flex flex-col items-center space-y-0">
                <span class="text-purple-400 text-xs">📄</span>
                <span class="font-medium text-xs text-center leading-tight">File 1</span>
            </div>
        </th>
    `;
    headerHTML += `
        <th class="px-1 py-1 text-left text-xs font-semibold text-purple-200 uppercase tracking-tight border-b border-purple-400/50">
            <div class="flex flex-col items-center space-y-0">
                <span class="text-purple-400 text-xs">📋</span>
                <span class="font-medium text-xs text-center leading-tight">File 2</span>
            </div>
        </th>
    `;
    headerHTML += '</tr></thead></table>';
    headersContainer.innerHTML = headerHTML;
}

// Render data for intermediate file (using Joint File Comparison format)
function renderIntermediateFileData(filteredData1, filteredData2, selectedMappings) {
    console.log('🎨 Starting renderIntermediateFileData...');
    console.log('Selected mappings:', selectedMappings);
    console.log('Filtered Data1 length:', filteredData1?.length);
    console.log('Filtered Data2 length:', filteredData2?.length);
    
    const dataContainer = document.getElementById('intermediateData');
    if (!dataContainer) {
        console.error('Data container not found!');
        return;
    }
    
    if (!filteredData1 || !filteredData2 || filteredData1.length === 0 || filteredData2.length === 0) {
        console.error('Filtered data is not available for rendering');
        dataContainer.innerHTML = '<div class="p-4 text-center text-gray-400">No data available</div>';
        return;
    }
    
    // Get original data from sideBySideData to use the Joint File Comparison logic
    if (!sideBySideData.file1Rows || !sideBySideData.file2Rows) {
        console.error('Side-by-side data not available for intermediate comparison');
        console.log('sideBySideData:', sideBySideData);
        return;
    }
    
    console.log('✅ sideBySideData available:');
    console.log('- file1Rows:', sideBySideData.file1Rows?.length);
    console.log('- file2Rows:', sideBySideData.file2Rows?.length);
    console.log('- data1Headers:', sideBySideData.data1Headers?.length);
    console.log('- data2Headers:', sideBySideData.data2Headers?.length);
    
    const data1Headers = sideBySideData.data1Headers;
    const data2Headers = sideBySideData.data2Headers;
    
    // Filter headers to only selected columns
    const selectedFile1Headers = [];
    const selectedFile2Headers = [];
    const selectedFile1Indices = [];
    const selectedFile2Indices = [];
    
    selectedMappings.forEach(mapping => {
        const file1Index = data1Headers.findIndex(h => h === mapping.file1Column);
        const file2Index = data2Headers.findIndex(h => h === mapping.file2Column);
        
        if (file1Index >= 0) {
            selectedFile1Headers.push(data1Headers[file1Index]);
            selectedFile1Indices.push(file1Index);
        }
        if (file2Index >= 0) {
            selectedFile2Headers.push(data2Headers[file2Index]);
            selectedFile2Indices.push(file2Index);
        }
    });
    
    // Create a mapping of all Account IDs (using Joint File Comparison logic)
    const allRowsMap = new Map();
    
    // Helper function to get Account ID from row data (same as Joint File Comparison)
    const getAccountIdFromRow = (rowData, headers) => {
        if (!rowData || !rowData.row) return null;
        const accountIdColumn = headers.findIndex(h => 
            h.toLowerCase().includes('account') && h.toLowerCase().includes('id')
        );
        const columnIndex = accountIdColumn >= 0 ? accountIdColumn : 0;
        return String(rowData.row[columnIndex] || '').trim();
    };
    
    // Process File 1 rows (same logic as Joint File Comparison)
    sideBySideData.file1Rows.forEach(rowData => {
        if (rowData.type === 'data') {
            const accountId = getAccountIdFromRow(rowData, data1Headers);
            if (accountId) {
                allRowsMap.set(accountId, {
                    accountId: accountId,
                    file1Data: rowData,
                    file2Data: null,
                    hasAccountMismatch: rowData.hasAccountMismatch
                });
            }
        }
    });
    
    // Process File 2 rows (same logic as Joint File Comparison)
    sideBySideData.file2Rows.forEach(rowData => {
        if (rowData.type === 'data') {
            const accountId = getAccountIdFromRow(rowData, data2Headers);
            if (accountId) {
                if (allRowsMap.has(accountId)) {
                    allRowsMap.get(accountId).file2Data = rowData;
                } else {
                    allRowsMap.set(accountId, {
                        accountId: accountId,
                        file1Data: null,
                        file2Data: rowData,
                        hasAccountMismatch: rowData.hasAccountMismatch
                    });
                }
            }
        }
    });
    
    // Sort rows by Account ID (same logic as Joint File Comparison)
    const sortedRows = Array.from(allRowsMap.values()).sort((a, b) => {
        const getAccountId = (data, headers) => {
            if (!data || !data.row) return '';
            const accountIdColumn = headers.findIndex(h => 
                h.toLowerCase().includes('account') && h.toLowerCase().includes('id')
            );
            const columnIndex = accountIdColumn >= 0 ? accountIdColumn : 0;
            return String(data.row[columnIndex] || '').trim();
        };
        
        const accountId1 = a.file1Data ? getAccountId(a.file1Data, data1Headers) : 
                          a.file2Data ? getAccountId(a.file2Data, data2Headers) : '';
        const accountId2 = b.file1Data ? getAccountId(b.file1Data, data1Headers) : 
                          b.file2Data ? getAccountId(b.file2Data, data2Headers) : '';
        
        return accountId1.localeCompare(accountId2);
    });
    
    // Create a simple two-column table only
    let tableHTML = '<table class="comparison-table border-2 border-purple-400/40"><tbody class="divide-y divide-purple-700/30">';
    
    // Render rows using Joint File Comparison format
    sortedRows.forEach((jointRow, rowIndex) => {
        const { accountId, file1Data, file2Data, hasAccountMismatch } = jointRow;
        
        // Check if we need a gap row for account mismatches (same as Joint File Comparison)
        if (hasAccountMismatch && file1Data && file2Data) {
            const totalColumns = selectedFile1Headers.length + selectedFile2Headers.length;
            tableHTML += `
                <tr class="bg-yellow-900/20 border-y-2 border-yellow-500/50 joint-account-mismatch" style="height: 40px;">
                    <td colspan="${totalColumns}" class="px-3 py-3 text-center text-yellow-300 text-xs font-medium">
                        ⚠️ Account ID/Number mismatch for Account ID "${accountId}" - data may not be related
                    </td>
                </tr>
            `;
        }
        
        // Create the main data row (same as Joint File Comparison)
        const baseRowClass = rowIndex % 2 === 0 ? 'bg-purple-900/10' : 'bg-purple-800/5';
        const accountMismatchClass = hasAccountMismatch ? 'border-l-4 border-yellow-400' : '';
        
        tableHTML += `<tr class="hover:bg-purple-700/20 transition-all duration-200 ${baseRowClass} ${accountMismatchClass} joint-view" style="height: 28px;">`;
        
            // Render exactly two cells for the first selected mapping pair
    const firstPair = selectedMappings && selectedMappings.length ? selectedMappings[0] : null;
    let colIndex1 = 0;
    let colIndex2 = 0;
    if (firstPair) {
        colIndex1 = data1Headers.findIndex(h => h === firstPair.file1Column);
        colIndex2 = data2Headers.findIndex(h => h === firstPair.file2Column);
        if (colIndex1 < 0) colIndex1 = 0;
        if (colIndex2 < 0) colIndex2 = 0;
    }

    const file1Value = file1Data ? (file1Data.row[colIndex1] || '') : '';
    const file2Value = file2Data ? (file2Data.row[colIndex2] || '') : '';

    tableHTML += `
        <td class="joint-view-file1-col professional-text transition-colors duration-200" style="min-width: ${getColumnWidth(data1Headers[colIndex1] || 'File 1', colIndex1)};">
            <div class="overflow-hidden" title="${escapeHtml(String(file1Value))}">
                ${escapeHtml(String(file1Value) || '-')}
            </div>
        </td>
    `;

    tableHTML += `
        <td class="joint-view-file2-col professional-text transition-colors duration-200" style="min-width: ${getColumnWidth(data2Headers[colIndex2] || 'File 2', colIndex2)};">
            <div class="overflow-hidden" title="${escapeHtml(String(file2Value))}">
                ${escapeHtml(String(file2Value) || '-')}
            </div>
        </td>
    `;
        
        tableHTML += '</tr>';
    });
    
    tableHTML += '</tbody></table>';
    
    console.log(`📊 Updating intermediate comparison with ${selectedFile1Headers.length + selectedFile2Headers.length} columns`);
    console.log('- Selected File 1 Headers:', selectedFile1Headers);
    console.log('- Selected File 2 Headers:', selectedFile2Headers);
    console.log('- Total sorted rows:', sortedRows.length);
    
    // Simply replace the content - no need for multiple tables or numbering
    dataContainer.innerHTML = tableHTML;
    
    console.log('✅ Intermediate comparison updated instantly');
    console.log('📋 Showing selected columns only');
}

// Helper function to find mapped column name
function findMappedColumnName(sourceHeader, sourceHeaders, targetHeaders) {
    // Try to find corresponding column using existing logic
    const correspondingIndex = findCorrespondingColumnIndex(sourceHeader, sourceHeaders, targetHeaders);
    if (correspondingIndex >= 0) {
        return targetHeaders[correspondingIndex];
    }
    return sourceHeader;
}

// Update intermediate file statistics
function updateIntermediateStats() {
    const statsElement = document.getElementById('intermediateStats');
    if (!statsElement) return;
    
    const selectedCount = selectedIntermediateColumns.size;
    const totalAvailable = parsedDataContent.mappingArray ? parsedDataContent.mappingArray.length : 0;
    
    statsElement.textContent = `${selectedCount} of ${totalAvailable} columns selected`;
}

// Show error message (extracted helper function)
function showError(message) {
    console.error('Error:', message);
    alert('Error: ' + message);
}

// Generate intermediate comparison result for a row
function generateIntermediateComparisonResult(file1Data, file2Data, data1Headers, data2Headers) {
    if (!file1Data || !file2Data) {
        return {
            summary: '<span class="text-red-400 font-bold">❌ Missing Data</span>',
            details: 'One or both files are missing data for this row'
        };
    }
    
    if (selectedIntermediateColumns.size === 0) {
        return {
            summary: '<span class="text-gray-400">No columns selected</span>',
            details: 'No columns have been selected for intermediate comparison'
        };
    }
    
    let matchedColumns = 0;
    let totalColumns = 0;
    let mismatches = [];
    
    // Check each selected column
    selectedIntermediateColumns.forEach(selectedColumn => {
        // Find the column index in file1
        const file1ColumnIndex = data1Headers.findIndex(header => 
            header.toLowerCase() === selectedColumn.toLowerCase()
        );
        
        if (file1ColumnIndex >= 0) {
            totalColumns++;
            
            // Find corresponding column in file2
            const correspondingFile2Index = findCorrespondingColumnIndex(
                data1Headers[file1ColumnIndex], 
                data1Headers, 
                data2Headers
            );
            
            if (correspondingFile2Index >= 0) {
                const file1Value = String(file1Data.row[file1ColumnIndex] || '').trim();
                const file2Value = String(file2Data.row[correspondingFile2Index] || '').trim();
                
                // Apply comparison settings (case-insensitive, ignore spaces)
                const ignoreSpaces = document.getElementById('ignoreSpaces')?.checked || false;
                const caseInsensitive = document.getElementById('caseInsensitive')?.checked || false;
                
                let normalizedFile1 = file1Value;
                let normalizedFile2 = file2Value;
                
                if (ignoreSpaces) {
                    normalizedFile1 = normalizedFile1.replace(/\s+/g, '');
                    normalizedFile2 = normalizedFile2.replace(/\s+/g, '');
                }
                
                if (caseInsensitive) {
                    normalizedFile1 = normalizedFile1.toLowerCase();
                    normalizedFile2 = normalizedFile2.toLowerCase();
                }
                
                if (normalizedFile1 === normalizedFile2) {
                    matchedColumns++;
                } else {
                    mismatches.push({
                        column: selectedColumn,
                        file1Value: file1Value,
                        file2Value: file2Value
                    });
                }
            } else {
                mismatches.push({
                    column: selectedColumn,
                    file1Value: String(file1Data.row[file1ColumnIndex] || ''),
                    file2Value: 'Column not found'
                });
            }
        }
    });
    
    // Generate summary
    const matchPercentage = totalColumns > 0 ? Math.round((matchedColumns / totalColumns) * 100) : 0;
    let summary = '';
    let summaryClass = '';
    
    if (matchPercentage === 100) {
        summary = `<span class="text-green-400 font-bold">✅ ${matchedColumns}/${totalColumns} Match</span>`;
        summaryClass = 'text-green-400';
    } else if (matchPercentage >= 50) {
        summary = `<span class="text-yellow-400 font-bold">⚠️ ${matchedColumns}/${totalColumns} Partial</span>`;
        summaryClass = 'text-yellow-400';
    } else {
        summary = `<span class="text-red-400 font-bold">❌ ${matchedColumns}/${totalColumns} Mismatch</span>`;
        summaryClass = 'text-red-400';
    }
    
    // Generate details
    let details = `Intermediate Comparison Results (${matchPercentage}% match):\n`;
    details += `Matched: ${matchedColumns}/${totalColumns} columns\n`;
    
    if (mismatches.length > 0) {
        details += '\nMismatches:\n';
        mismatches.forEach(mismatch => {
            details += `• ${mismatch.column}: "${mismatch.file1Value}" vs "${mismatch.file2Value}"\n`;
        });
    }
    
    return {
        summary: summary,
        details: details
    };
}

// Simple direct function to set up browse buttons
function setupBrowseButtonsDirectly() {
    console.log('🚀 Setting up browse buttons with direct approach...');
    
    // Find all browse buttons
    const browseButtons = document.querySelectorAll('[data-path-load]');
    console.log('Found browse buttons:', browseButtons.length);
    
    browseButtons.forEach((button, index) => {
        const fileKey = button.getAttribute('data-path-load');
        console.log(`Setting up button ${index + 1} for ${fileKey}`);
        
        // Remove any existing listeners
        button.onclick = null;
        
        // Add direct onclick handler
        button.onclick = function(e) {
            console.log('🔥 DIRECT ONCLICK - Browse button clicked for:', fileKey);
            e.preventDefault();
            e.stopPropagation();
            
            // Find the corresponding file input
            const fileInput = document.querySelector(`input[data-file="${fileKey}"]`);
            console.log('Found file input:', !!fileInput);
            
            if (fileInput) {
                console.log('Triggering file input click...');
                fileInput.click();
            } else {
                console.error('File input not found for:', fileKey);
            }
        };
        
        // Also add addEventListener as backup
        button.addEventListener('click', function(e) {
            console.log('🔥 ADDEVENTLISTENER - Browse button clicked for:', fileKey);
        });
        
        console.log(`✅ Browse button ${index + 1} setup complete`);
    });
}

// Debug panel functions
function showDebug() {
    const panel = document.getElementById('debugPanel');
    if (panel) {
        panel.classList.remove('hidden');
    }
}

function toggleDebug() {
    const panel = document.getElementById('debugPanel');
    if (panel) {
        panel.classList.add('hidden');
    }
}

function testBrowseButtons() {
    console.log('🧪 TESTING BROWSE BUTTONS...');
    
    const browseButtons = document.querySelectorAll('[data-path-load]');
    console.log('Found buttons:', browseButtons.length);
    
    browseButtons.forEach((button, index) => {
        const fileKey = button.getAttribute('data-path-load');
        console.log(`Button ${index + 1}: ${fileKey}`, button);
        console.log('Button onclick:', typeof button.onclick);
        console.log('Button has event listeners:', button.onclick !== null);
        
        // Test click programmatically
        console.log(`Testing click for button ${index + 1}...`);
        try {
            button.click();
        } catch (error) {
            console.error('Error clicking button:', error);
        }
    });
}

function updateDebugStatus(elementId, text, className = 'text-yellow-400') {
    const element = document.getElementById(elementId);
    if (element) {
        element.textContent = text;
        element.className = className;
    }
}

function updateFilesStatus() {
    const uploadedCount = Object.values(uploadedFiles).filter(file => file !== null).length;
    updateDebugStatus('filesStatus', `${uploadedCount}/5`, uploadedCount === 5 ? 'text-green-400' : 'text-yellow-400');
}

// Test file generation functions
function generateTestFiles() {
    console.log('Generating test files...');
    
    try {
        // Create test schema files matching your scenario
        const schema1Data = [
            ['Column Name', 'Data Type'],
            ['Account holder Name', 'Text'],
            ['Account ID', 'Number'],
            ['Closing Balance', 'Currency'],
            ['Last Transaction Status', 'Text'],
            ['Last Update Date', 'Date']
        ];
        
        const schema2Data = [
            ['Column Name', 'Data Type'],
            ['Account Number', 'Number'],
            ['Account Holder Name', 'Text'],
            ['Last Update Date', 'Date'],
            ['Last Transaction Status', 'Text'],
            ['Closing Balance', 'Currency']
        ];
        
        // Create test data files matching the schema structure
        const data1Content = [
            ['1001', 'John Smith', '150.00', 'Active', '2024-01-15'],
            ['1002', 'Sarah Wilson', '275.50', 'Active', '2024-01-16'],
            ['1003', 'Mike Johnson', '320.75', 'Pending', '2024-01-17'],
            ['1004', 'Emily Davis', '180.25', 'Active', '2024-01-18'],
            ['1005', 'David Brown', '425.00', 'Completed', '2024-01-19']
        ];

        const data2Content = [
            ['ACC1001', 'John Smith', '2024-01-15', 'Active', '150.00'],
            ['ACC1002', 'Sarah Wilson', '2024-01-16', 'Active', '275.50'],
            ['ACC1003', 'Michael Johnson', '2024-01-17', 'Pending', '320.75'],
            ['ACC1004', 'Emily Davis', '2024-01-18', 'Active', '185.25'],
            ['ACC1006', 'Robert Taylor', '2024-01-20', 'Completed', '520.00']
        ];
        
        // Create mapping file based on your scenario
        const mappingData = [
            ['Data File 1 Column', 'Data File 2 Column'],
            ['Account holder Name', 'Account Holder Name'],
            ['Account ID', 'Account Number'],
            ['Closing Balance', 'Closing Balance'],
            ['Last Transaction Status', 'Last Transaction Status'],
            ['Last Update Date', 'Last Update Date']
        ];
        
        // Generate Excel files for schemas and mapping
        const wb1 = XLSX.utils.book_new();
        const ws1 = XLSX.utils.aoa_to_sheet(schema1Data);
        XLSX.utils.book_append_sheet(wb1, ws1, 'Schema');
        const schema1Blob = new Blob([XLSX.write(wb1, {type: 'array', bookType: 'xlsx'})], {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'});
        
        const wb2 = XLSX.utils.book_new();
        const ws2 = XLSX.utils.aoa_to_sheet(schema2Data);
        XLSX.utils.book_append_sheet(wb2, ws2, 'Schema');
        const schema2Blob = new Blob([XLSX.write(wb2, {type: 'array', bookType: 'xlsx'})], {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'});
        
        const wb3 = XLSX.utils.book_new();
        const ws3 = XLSX.utils.aoa_to_sheet(mappingData);
        XLSX.utils.book_append_sheet(wb3, ws3, 'Mapping');
        const mappingBlob = new Blob([XLSX.write(wb3, {type: 'array', bookType: 'xlsx'})], {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'});
        
        // Generate CSV files for data
        const data1CSV = data1Content.map(row => row.join(',')).join('\n');
        const data2CSV = data2Content.map(row => row.join(',')).join('\n');
        
        const data1Blob = new Blob([data1CSV], {type: 'text/csv'});
        const data2Blob = new Blob([data2CSV], {type: 'text/csv'});
        
        // Create File objects
        const testFiles = {
            schema1: new File([schema1Blob], 'test_schema1.xlsx', {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'}),
            schema2: new File([schema2Blob], 'test_schema2.xlsx', {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'}),
            data1: new File([data1Blob], 'test_data1.csv', {type: 'text/csv'}),
            data2: new File([data2Blob], 'test_data2.csv', {type: 'text/csv'}),
            mapping: new File([mappingBlob], 'test_mapping.xlsx', {type: 'application/vnd.openxmlformats-officedocument.spreadsheetml.sheet'})
        };
        
        // Upload test files automatically
        uploadTestFiles(testFiles);
        
    } catch (error) {
        console.error('Error generating test files:', error);
        showError('Error generating test files: ' + error.message);
    }
}

async function uploadTestFiles(testFiles) {
    console.log('Uploading test files...');
    
    try {
        for (let step = 1; step <= 5; step++) {
            const fileKey = getFileKey(step);
            const file = testFiles[fileKey];
            const uploadArea = document.querySelector(`[data-step="${step}"]`);
            const statusDiv = uploadArea?.querySelector('.file-status');
            
            if (file && uploadArea && statusDiv) {
                console.log(`Uploading test file for step ${step}:`, file.name);
                await handleFileUpload(file, step, uploadArea, statusDiv);
                
                // Wait a bit between uploads to see the progress
                await new Promise(resolve => setTimeout(resolve, 500));
            }
        }
        
        showSuccessNotification('Test files uploaded successfully! You can now test the comparison functionality.');
        
    } catch (error) {
        console.error('Error uploading test files:', error);
        showError('Error uploading test files: ' + error.message);
    }
}

function initializeFileUploads() {
    console.log('Setting up file upload functionality...');
    
    // Get all upload areas
    const uploadAreas = document.querySelectorAll('.neo-upload');
    console.log('Processing', uploadAreas.length, 'upload areas');
    
    uploadAreas.forEach((area, index) => {
        const step = parseInt(area.getAttribute('data-step'));
        const fileInput = area.querySelector('input[type="file"]');
        const statusDiv = area.querySelector('.file-status');
        const browseButton = area.querySelector('[data-path-load]');
        
        console.log(`Setting up upload area ${step}:`, {
            area: !!area,
            fileInput: !!fileInput,
            statusDiv: !!statusDiv,
            browseButton: !!browseButton,
            step: step
        });
        
        if (!fileInput) {
            console.error(`No file input found for step ${step}`);
            return;
        }
        
        if (!statusDiv) {
            console.error(`No status div found for step ${step}`);
            return;
        }
        
        // Browse button setup is now handled in setupBrowseButtonsDirectly()
        console.log(`📝 Browse button for step ${step} will be handled by direct setup`);
        
        // File selection handler (works for both browse and direct click)
        // Remove existing listeners first
        fileInput.removeEventListener('change', fileInput.changeHandler);
        
        fileInput.changeHandler = function(e) {
            console.log(`🔥 FILE SELECTED for step ${step}:`, e.target.files[0]);
            
            if (e.target.files.length > 0) {
                const file = e.target.files[0];
                console.log('File details:', {
                    name: file.name,
                    size: file.size,
                    type: file.type
                });
                
                // Update the path input with file information
                const pathInput = area.querySelector('[data-path-input]');
                if (pathInput) {
                    const fileInfo = `${file.name} (${(file.size / 1024).toFixed(1)} KB)`;
                    pathInput.value = fileInfo;
                    console.log('Updated path input:', fileInfo);
                }
                
                handleFileUpload(file, step, area, statusDiv);
            }
        };
        
        fileInput.addEventListener('change', fileInput.changeHandler);
        
        // Drag and drop functionality
        area.addEventListener('dragover', function(e) {
            e.preventDefault();
            if (!area.classList.contains('disabled')) {
                area.style.borderColor = '#00d4ff';
                area.style.backgroundColor = 'rgba(0, 212, 255, 0.1)';
                console.log(`Drag over area ${step}`);
            }
        });
        
        area.addEventListener('dragleave', function(e) {
            e.preventDefault();
            area.style.borderColor = '';
            area.style.backgroundColor = '';
        });
        
        area.addEventListener('drop', function(e) {
            e.preventDefault();
            area.style.borderColor = '';
            area.style.backgroundColor = '';
            
            if (!area.classList.contains('disabled') && e.dataTransfer.files.length > 0) {
                const file = e.dataTransfer.files[0];
                console.log(`File dropped on area ${step}:`, file.name);
                
                // Update the path input with file information
                const pathInput = area.querySelector('[data-path-input]');
                if (pathInput) {
                    const fileInfo = `${file.name} (${(file.size / 1024).toFixed(1)} KB)`;
                    pathInput.value = fileInfo;
                }
                
                handleFileUpload(file, step, area, statusDiv);
            }
        });
        
        console.log(`Setup complete for upload area ${step}`);
    });
}

function enableStep(step) {
    console.log('=== ENABLING STEP ===', step);
    
    const stepArea = document.querySelector(`[data-step="${step}"]`);
    console.log('Found step area:', !!stepArea);
    
    if (stepArea) {
        console.log('Enabling step area', step);
        
        // Remove disabled state
        stepArea.classList.remove('disabled');
        stepArea.removeAttribute('disabled');
        
        // Update visual state
        stepArea.style.opacity = '1';
        stepArea.style.cursor = 'pointer';
        stepArea.style.pointerEvents = 'auto';
        
        // Update status message
        const statusDiv = stepArea.querySelector('.file-status');
        if (statusDiv && step > 1) {
            statusDiv.textContent = 'Enter the Path';
            statusDiv.className = 'file-status text-sm font-medium text-yellow-400';
        }
        
        // Enable file input
        const fileInput = stepArea.querySelector('input[type="file"]');
        if (fileInput) {
            fileInput.disabled = false;
            console.log('File input enabled for step', step);
        }
        
        // Enable path input and load button
        const pathInput = stepArea.querySelector('[data-path-input]');
        const loadButton = stepArea.querySelector('[data-path-load]');
        
        if (pathInput) {
            pathInput.disabled = false;
            pathInput.style.opacity = '1';
        }
        
        if (loadButton) {
            loadButton.disabled = false;
            loadButton.style.opacity = '1';
        }
        
        console.log('Step', step, 'enabled successfully');
    } else {
        console.error('Could not find step area for step:', step);
    }
}

function enableCompareButtons() {
    console.log('=== ENABLING COMPARE BUTTONS ===');
    
    const compareBtn = document.getElementById('compareBtn');
    const generateIntermediateBtn = document.getElementById('generateIntermediateBtn');
    
    console.log('Compare button found:', !!compareBtn);
    console.log('Generate intermediate button found:', !!generateIntermediateBtn);
    
    if (compareBtn) {
        compareBtn.disabled = false;
        compareBtn.classList.remove('opacity-50', 'cursor-not-allowed');
        console.log('Compare button enabled');
    }
    
    if (generateIntermediateBtn) {
        generateIntermediateBtn.disabled = false;
        generateIntermediateBtn.classList.remove('opacity-50', 'cursor-not-allowed');
        console.log('Generate intermediate button enabled');
    }
    
    // Show success message
    showSuccessNotification('All files uploaded! You can now run comparisons.');
}

// Enhanced event listener initialization with multiple approaches
function initializeEventListeners() {
    console.log('=== Initializing event listeners ===');
    
    // Compare button
    const compareBtn = document.getElementById('compareBtn');
    console.log('Compare button found:', !!compareBtn);
    if (compareBtn) {
        compareBtn.addEventListener('click', performComparison);
        console.log('✓ Compare button event listener added');
    }
    
    // Generate Intermediate button - MULTIPLE ATTACHMENT METHODS
    const generateBtn = document.getElementById('generateIntermediateBtn');
    console.log('Generate intermediate button found:', !!generateBtn);
    console.log('Generate intermediate button element:', generateBtn);
    
    if (generateBtn) {
        // Method 1: Remove any existing listeners and add new one
        generateBtn.removeEventListener('click', performIntermediateComparison);
        generateBtn.addEventListener('click', function(event) {
            console.log('🔥 INTERMEDIATE BUTTON CLICKED! 🔥');
            console.log('Event:', event);
            
            // Check if button is disabled
            if (generateBtn.disabled) {
                console.log('❌ Button is disabled, ignoring click');
                alert('Button is disabled. Please ensure data files are processed first.');
                return;
            }
            
            console.log('✅ Button is enabled, calling performIntermediateComparison...');
            try {
                performIntermediateComparison();
            } catch (error) {
                console.error('Error calling performIntermediateComparison:', error);
                alert('Error: ' + error.message);
            }
        });
        
        // Method 2: Direct onclick property as backup
        generateBtn.onclick = function(event) {
            console.log('🔥 INTERMEDIATE BUTTON ONCLICK TRIGGERED! 🔥');
            event.preventDefault();
            if (!generateBtn.disabled) {
                try {
                    performIntermediateComparison();
                } catch (error) {
                    console.error('Error in onclick handler:', error);
                    alert('Onclick Error: ' + error.message);
                }
            } else {
                console.log('Button disabled in onclick handler');
            }
        };
        
        // Method 3: Add global function for manual testing
        window.triggerIntermediateComparison = function() {
            console.log('🔥 MANUAL TRIGGER ACTIVATED! 🔥');
            if (generateBtn.disabled) {
                console.log('Button is disabled, enabling it temporarily...');
                generateBtn.disabled = false;
            }
            performIntermediateComparison();
        };
        
        console.log('✓ Generate intermediate button ALL event listeners added');
        console.log('✓ Manual trigger function created: triggerIntermediateComparison()');
    } else {
        console.error('❌ Generate intermediate button NOT FOUND!');
    }
    
    // Download buttons
    const downloadMismatchedBtn = document.getElementById('downloadMismatchedBtn');
    if (downloadMismatchedBtn) {
        downloadMismatchedBtn.addEventListener('click', downloadMismatchedRecords);
        console.log('✓ Download mismatched button event listener added');
    }
    
    const downloadComparedBtn = document.getElementById('downloadComparedBtn');
    if (downloadComparedBtn) {
        downloadComparedBtn.addEventListener('click', downloadComparedFileAllRows);
        console.log('✓ Download compared button event listener added');
    }
    
    const downloadComparisonBtn = document.getElementById('downloadComparisonBtn');
    if (downloadComparisonBtn) {
        downloadComparisonBtn.addEventListener('click', downloadComparisonResults);
        console.log('✓ Download comparison results button event listener added');
    }
    
    const downloadIntermediateBtn = document.getElementById('downloadIntermediateBtn');
    if (downloadIntermediateBtn) {
        downloadIntermediateBtn.addEventListener('click', downloadIntermediateAllRows);
        console.log('✓ Download intermediate button event listener added');
    }
    
    const downloadIntermediateFilesBtn = document.getElementById('downloadIntermediateFilesBtn');
    if (downloadIntermediateFilesBtn) {
        downloadIntermediateFilesBtn.addEventListener('click', downloadIntermediateFiles);
        console.log('✓ Download intermediate files button event listener added');
    }
    
    // Tab functionality
    const originalTab = document.getElementById('originalTab');
    const intermediateTab = document.getElementById('intermediateTab');
    
    if (originalTab && intermediateTab) {
        originalTab.addEventListener('click', () => {
            console.log('Switching to original tab');
            
            // Update tab appearance
            originalTab.classList.add('border-cyan-400', 'text-cyan-400', 'text-glow-cyan');
            originalTab.classList.remove('border-transparent', 'text-gray-400');
            
            intermediateTab.classList.add('border-transparent', 'text-gray-400');
            intermediateTab.classList.remove('border-cyan-400', 'text-cyan-400', 'text-glow-cyan');
            
            // Show/hide content
            document.getElementById('originalComparison').classList.remove('hidden');
            document.getElementById('intermediateComparison').classList.add('hidden');
        });
        
        intermediateTab.addEventListener('click', () => {
            console.log('Switching to intermediate tab');
            
            // Update tab appearance
            intermediateTab.classList.add('border-cyan-400', 'text-cyan-400', 'text-glow-cyan');
            intermediateTab.classList.remove('border-transparent', 'text-gray-400');
            
            originalTab.classList.add('border-transparent', 'text-gray-400');
            originalTab.classList.remove('border-cyan-400', 'text-cyan-400', 'text-glow-cyan');
            
            // Show/hide content
            document.getElementById('intermediateComparison').classList.remove('hidden');
            document.getElementById('originalComparison').classList.add('hidden');
            
            // Update intermediate button state when tab is accessed
            console.log('Updating intermediate button state on tab switch...');
            updateIntermediateButtonState();
            
            // Update intermediate tab with mapping information if available
            if (uploadedFiles.mapping) {
                console.log('Mapping file found, updating intermediate display...');
                setTimeout(() => {
                updateIntermediateTabWithMapping();
                }, 500); // Small delay to ensure file is processed
            } else {
                console.log('No mapping file uploaded yet');
            }
        });
        
        console.log('✓ Tab event listeners added');
    }
    
    // Note: Browse button setup is now handled in initializeFileUploads()
    // after the DOM elements are replaced to avoid event listener removal
    
    console.log('=== Event listeners initialization complete ===');
}

async function handleFileUpload(file, step, area, statusDiv) {
    console.log('=== HANDLING FILE UPLOAD ===');
    console.log('File:', file.name);
    console.log('Step:', step);
    console.log('Area:', area);
    console.log('Status div:', statusDiv);
    
    if (!file) {
        console.error('No file provided');
        return;
    }
    
    // Validate file extension
    const fileType = getFileType(step);
    const expectedExtensions = getExpectedExtensions(step);
    const fileName = file.name.toLowerCase();
    
    console.log('Expected extensions:', expectedExtensions);
    console.log('File name:', fileName);
    
    const isValidExtension = expectedExtensions.some(ext => fileName.endsWith(ext));
    
    if (!isValidExtension) {
        const errorMessage = `Invalid file type. Please select a ${fileType} file with ${expectedExtensions.join('/')} extension.`;
        console.error(errorMessage);
        showError(errorMessage);
        
        if (statusDiv) {
            statusDiv.textContent = 'Invalid file type';
            statusDiv.className = 'file-status text-sm font-medium text-red-400';
        }
        return;
    }
    
    try {
        console.log('File validation passed. Processing...');
        
        // Show loading state
        if (statusDiv) {
            statusDiv.textContent = 'Processing...';
            statusDiv.className = 'file-status text-sm font-medium text-blue-400';
        }
        
        // Store file
        const fileKey = getFileKey(step);
        uploadedFiles[fileKey] = file;
        console.log('File stored with key:', fileKey);
        
        // Process data files to extract column information (steps 3 and 4)
        if (step === 3 || step === 4) {
            console.log('Processing data file for column extraction...');
            try {
                const dataContent = await parseDataFile(file);
                console.log('Data file parsed, rows:', dataContent.length);
                
                if (dataContent.length > 0) {
                    // First check if corresponding schema file is available to get proper headers
                    const schemaKey = step === 3 ? 'schema1' : 'schema2';
                    let headers = dataContent[0]; // Default to original headers
                    
                    if (uploadedFiles[schemaKey]) {
                        console.log(`Schema file ${schemaKey} available, extracting proper column names...`);
                        try {
                            const schemaData = await parseExcelFile(uploadedFiles[schemaKey]);
                            const schemaColumns = extractColumnMetadata(schemaData);
                            const schemaHeaders = Object.keys(schemaColumns);
                            
                            if (schemaHeaders.length > 0) {
                                headers = schemaHeaders;
                                console.log(`Using schema headers for ${step === 3 ? 'file1' : 'file2'}:`, headers);
                            }
                        } catch (error) {
                            console.warn('Error parsing schema file for headers:', error);
                            // Fall back to original headers
                        }
                    }
                    
                    console.log('Final headers for column selection:', headers);
                    
                    if (step === 3) {
                        availableColumns.file1 = headers;
                        updateColumnSelection('file1', headers);
                    } else {
                        availableColumns.file2 = headers;
                        updateColumnSelection('file2', headers);
                    }
                    console.log('Column selection updated for', step === 3 ? 'file1' : 'file2');
                }
            } catch (error) {
                console.error('Error parsing data file:', error);
                // Don't fail the upload for column extraction errors
            }
        }
        if (area) {
            area.classList.add('active');
            area.classList.remove('disabled');
            area.style.borderColor = '#10b981';
            area.style.backgroundColor = 'rgba(16, 185, 129, 0.1)';
        }
        
        if (statusDiv) {
            statusDiv.textContent = `✓ ${file.name}`;
            statusDiv.className = 'file-status text-sm font-medium text-green-400';
        }
        
        console.log('File upload successful for step:', step);
        
        // Check if all files are uploaded
        if (step === totalSteps) {
            console.log('All files uploaded, enabling compare buttons');
            enableCompareButtons();
        }
        
        // Update current step
        currentStep = Math.max(currentStep, step + 1);
        console.log('Current step updated to:', currentStep);
        
        // Update debug status
        updateDebugStatus('currentStepStatus', currentStep.toString(), 'text-cyan-400');
        updateFilesStatus()
        // Show success notification
        showSuccessNotification(`File "${file.name}" uploaded successfully!`);
        
        // Call specific functions based on file type
        if (step === 3 || step === 4) {
            // For data files, extract headers and update column selection
            parseDataFile(file).then(content => {
                if (content.length > 0) {
                    updateColumnSelection(step, content[0]);
                }
            }).catch(error => {
                console.error(`Error parsing ${fileKey}:`, error);
            });
        }
        
        if (step === 5) {
                    // For mapping file, update intermediate tab display
        console.log('Mapping file uploaded, updating intermediate tab...');
        setTimeout(() => {
            updateIntermediateTabWithMapping();
            // Auto-select requested intermediate pairs if present in mapping
            setTimeout(autoSelectPreferredIntermediateColumns, 400);
        }, 500); // Small delay to ensure file is processed
        }
        
    } catch (error) {
        console.error('Error processing file:', error);
        showError(`Error processing file: ${error.message}`);
        
        if (statusDiv) {
            statusDiv.textContent = 'Error - try again';
            statusDiv.className = 'file-status text-sm font-medium text-red-400';
        }
    }
}

function getFileType(step) {
    const types = {
        1: 'Schema',
        2: 'Schema',
        3: 'Data',
        4: 'Data',
        5: 'Mapping'
    };
    return types[step] || 'File';
}
function getExpectedExtensions(step) {
    const extensions = {
        1: ['.xlsx'],
        2: ['.xlsx'],
        3: ['.csv', '.txt'],
        4: ['.csv', '.txt'],
        5: ['.xlsx']
    };
    return extensions[step] || [];
}
function getFileKey(step) {
    const keys = {
        1: 'schema1',
        2: 'schema2',
        3: 'data1',
        4: 'data2',
        5: 'mapping'
    };
    return keys[step];
}

/**
 * Updates the column selection dropdowns in the UI.
 * @param {string} fileKey 'file1' or 'file2'
 * @param {string[]} headers An array of column names (headers).
 */
function updateColumnSelection(fileKey, headers) {
    console.log(`Updating column selection for ${fileKey} with headers:`, headers);

    const selectId = fileKey === 'file1' ? 'file1KeyColumn' : 'file2KeyColumn';
    const selectElement = document.getElementById(selectId);

    if (!selectElement) {
        console.error(`Column selection dropdown not found for ID: ${selectId}`);
        return;
    }
    
    // Clear existing options
    selectElement.innerHTML = '';
    
    // Add a default, disabled option
    const defaultOption = document.createElement('option');
    defaultOption.value = '';
    defaultOption.textContent = `-- Select Key Column --`;
    defaultOption.disabled = true;
    defaultOption.selected = true;
    selectElement.appendChild(defaultOption);

    // Add new options based on the headers
    headers.forEach(header => {
            const option = document.createElement('option');
        option.value = header;
        option.textContent = header;
        selectElement.appendChild(option);
    });

    // Store the headers in the global state
    if (fileKey === 'file1') {
        availableColumns.file1 = headers;
            } else {
        availableColumns.file2 = headers;
    }
    
    console.log(`Column selection for ${fileKey} updated successfully.`);
}

// Stubs for other functions referenced in the code
// You will need to implement these based on your application's logic.

async function parseDataFile(file) {
    console.log(`Parsing data file: ${file.name}`);
    // A simplified parser for CSV. You might need a more robust library.
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = (e) => {
            const text = e.target.result;
            const lines = text.split('\n').map(line => line.split(',').map(cell => cell.trim()));
            resolve(lines);
        };
        reader.onerror = (e) => reject(e.target.error);
        reader.readAsText(file);
    });
}

async function parseExcelFile(file) {
    console.log(`Parsing Excel file: ${file.name}`);
    // This requires a library like 'xlsx.js' to be included in your HTML
    return new Promise((resolve, reject) => {
        const reader = new FileReader();
        reader.onload = (e) => {
            const data = new Uint8Array(e.target.result);
            const workbook = XLSX.read(data, { type: 'array' });
            const sheetName = workbook.SheetNames[0];
            const worksheet = workbook.Sheets[sheetName];
            const jsonSheet = XLSX.utils.sheet_to_json(worksheet, { header: 1 });
            resolve(jsonSheet);
        };
        reader.onerror = (e) => reject(e.target.error);
        reader.readAsArrayBuffer(file);
    });
}

function extractColumnMetadata(schemaData) {
    console.log('Extracting column metadata from schema data...');
    const metadata = {};
    if (schemaData.length > 1) {
        // Assuming the first row is headers and subsequent rows are data
        const headers = schemaData[0];
        const dataRows = schemaData.slice(1);
        
        dataRows.forEach(row => {
            const columnName = row[0]; // Assuming first column is 'Column Name'
            const dataType = row[1];   // Assuming second column is 'Data Type'
            if (columnName) {
                metadata[columnName] = dataType || 'Unknown';
            }
        });
    }
    console.log('Extracted metadata:', metadata);
    return metadata;
}

function processMappingFile(mappingData) {
    const mappings = {};
    
    // Skip header row and process mappings
    for (let i = 1; i < mappingData.length; i++) {
        const row = mappingData[i];
        if (row.length >= 2) {
            const file1Column = row[0]?.toString().trim();
            const file2Column = row[1]?.toString().trim();
            if (file1Column && file2Column) {
                mappings[file1Column] = file2Column;
            }
        }
    }
    
    return mappings;
}

function normalizeValue(value, ignoreSpaces, caseInsensitive) {
    let normalized = value?.toString() || '';
    
    if (ignoreSpaces) {
        normalized = normalized.trim();
    }
    
    if (caseInsensitive) {
        normalized = normalized.toLowerCase();
    }
    
    return normalized;
}

function showLoading(show) {
    const loadingOverlay = document.getElementById('loadingOverlay');
    if (loadingOverlay) {
        if (show) {
            loadingOverlay.classList.remove('hidden');
        } else {
            loadingOverlay.classList.add('hidden');
        }
    }
}

// Removed duplicate showError function - using the one above

function showSuccessNotification(message) {
    console.log('Success:', message);
    // You could implement a toast notification here
}

// Side-by-side comparison functions
function displaySideBySideComparison(results) {
    console.log('Displaying joint side-by-side comparison...');
    
    // Hide old table and show side-by-side layout
    document.getElementById('resultsTable').innerHTML = '';
    document.getElementById('sideBySideComparison').classList.remove('hidden');
    
    // Show column selection panel if not in intermediate comparison mode
    console.log('🔍 Checking if should show column selection:', {
        intermediateComparisonActive,
        hasMappingArray: !!parsedDataContent.mappingArray
    });
    
    if (!intermediateComparisonActive && parsedDataContent.mappingArray) {
        console.log('✅ Showing intermediate column selection');
        showIntermediateColumnSelection();
    }
    
    // Ensure File 2 panel is completely hidden
    const file2Panel = document.getElementById('file2Panel');
    if (file2Panel) {
        file2Panel.classList.add('hidden');
        file2Panel.style.display = 'none';
    }
    
    // Additional fallback to hide any File 2 panels
    const file2Panels = document.querySelectorAll('#sideBySideComparison .bg-green-900\\/20, #sideBySideComparison .from-green-900\\/20');
    file2Panels.forEach(panel => {
        panel.classList.add('hidden');
        panel.style.display = 'none';
    });
    
    // Ensure File 1 panel takes full width
    const file1Panel = document.querySelector('#sideBySideComparison .bg-cyan-900\\/20');
    if (file1Panel) {
        file1Panel.style.width = '100%';
        file1Panel.style.maxWidth = '100%';
    }
    
    // Prepare the comparison data
    prepareSideBySideData(results);
    
    // Render the comparison
    renderSideBySideHeaders();
    renderSideBySideData();
    
    // Update stats after rendering is complete
    updateComparisonStats();
    
    // Force update after a brief delay to ensure all DOM elements are rendered
    setTimeout(() => {
        updateComparisonStats();
        console.log('Visual comparison stats updated');
    }, 200);
}

function prepareSideBySideData(results) {
    const data1Content = parsedDataContent.data1;
    const data2Content = parsedDataContent.data2;
    const columnMappings = parsedDataContent.columnMappings;
    
    if (!data1Content || !data2Content) {
        console.error('Data content not available for side-by-side comparison');
        return;
    }
    
    // Get headers and data rows (prefer schema-based overrides if width matches)
    const rawHeaders1 = data1Content[0] || [];
    const rawHeaders2 = data2Content[0] || [];
    const data1Headers = (parsedDataContent.overrideHeaders1 && parsedDataContent.overrideHeaders1.length === rawHeaders1.length)
        ? parsedDataContent.overrideHeaders1
        : rawHeaders1;
    const data2Headers = (parsedDataContent.overrideHeaders2 && parsedDataContent.overrideHeaders2.length === rawHeaders2.length)
        ? parsedDataContent.overrideHeaders2
        : rawHeaders2;
    const data1Rows = data1Content.slice(1);
    const data2Rows = data2Content.slice(1);
    
    // Find Account ID and Account Number column indexes
    sideBySideData.accountIdColumn = findAccountIdColumn(data1Headers, data2Headers, columnMappings);
    sideBySideData.accountNumberColumn = findAccountNumberColumn(data1Headers, data2Headers, columnMappings);
    
    // Helper to safely get the row key using mapped columns or header names
    const getRowKey = (row, isFile1) => {
        // 1) Prefer mapped Account ID column
        if (sideBySideData.accountIdColumn) {
            const idx = isFile1 ? sideBySideData.accountIdColumn.file1Index : sideBySideData.accountIdColumn.file2Index;
            if (typeof idx === 'number' && idx >= 0) {
                const v = row[idx];
                if (v !== undefined && String(v).trim() !== '') return String(v).trim();
            }
        }
        // 2) Fall back to mapped Account Number column
        if (sideBySideData.accountNumberColumn) {
            const idx = isFile1 ? sideBySideData.accountNumberColumn.file1Index : sideBySideData.accountNumberColumn.file2Index;
            if (typeof idx === 'number' && idx >= 0) {
                const v = row[idx];
                if (v !== undefined && String(v).trim() !== '') return String(v).trim();
            }
        }
        // 3) Try to locate by header name (Account + Id)
        const headers = isFile1 ? data1Headers : data2Headers;
        let byNameIdx = headers.findIndex(h => h && h.toLowerCase().includes('account') && h.toLowerCase().includes('id'));
        if (byNameIdx < 0) {
            // As a last resort, try Account + Number
            byNameIdx = headers.findIndex(h => h && h.toLowerCase().includes('account') && h.toLowerCase().includes('number'));
        }
        if (byNameIdx >= 0) {
            const v = row[byNameIdx];
            if (v !== undefined) return String(v).trim();
        }
        // 4) Final fallback to first column
        return String(row[0] || '').trim();
    };
    
    // Create ID-based mappings using the detected key column
    const data1ByID = {};
    const data2ByID = {};
    
    data1Rows.forEach((row, index) => {
        const id = getRowKey(row, true);
        if (id) {
            data1ByID[id] = { row, originalIndex: index };
        }
    });
    
    data2Rows.forEach((row, index) => {
        const id = getRowKey(row, false);
        if (id) {
            data2ByID[id] = { row, originalIndex: index };
        }
    });
    
    // Create aligned data for display
    const alignedData = createAlignedRowData(data1ByID, data2ByID, results);
    
    sideBySideData.file1Rows = alignedData.file1Rows;
    sideBySideData.file2Rows = alignedData.file2Rows;
    sideBySideData.rowMappings = alignedData.mappings;
    sideBySideData.data1Headers = data1Headers;
    sideBySideData.data2Headers = data2Headers;
    
    // Make sideBySideData available globally for download function
    window.sideBySideData = sideBySideData;
}

function findAccountIdColumn(data1Headers, data2Headers, columnMappings) {
    // Look for Account ID in the mappings
    if (columnMappings) {
        for (const [file1Col, file2Col] of Object.entries(columnMappings)) {
            if (file1Col.toLowerCase().includes('account') && file1Col.toLowerCase().includes('id')) {
                const file1Index = data1Headers.findIndex(h => h.trim() === file1Col.trim());
                const file2Index = data2Headers.findIndex(h => h.trim() === file2Col.trim());
                return { file1Index, file2Index, file1Col, file2Col };
            }
        }
    }
    return null;
}

function findAccountNumberColumn(data1Headers, data2Headers, columnMappings) {
    // Look for Account Number in the mappings
    if (columnMappings) {
        for (const [file1Col, file2Col] of Object.entries(columnMappings)) {
            if (file1Col.toLowerCase().includes('account') && file1Col.toLowerCase().includes('number')) {
                const file1Index = data1Headers.findIndex(h => h.trim() === file1Col.trim());
                const file2Index = data2Headers.findIndex(h => h.trim() === file2Col.trim());
                return { file1Index, file2Index, file1Col, file2Col };
            }
        }
    }
    return null;
}

function createAlignedRowData(data1ByID, data2ByID, results) {
    const file1Rows = [];
    const file2Rows = [];
    const mappings = [];
    
    // Get all unique IDs from both files
    const allIDs = new Set([...Object.keys(data1ByID), ...Object.keys(data2ByID)]);
    const sortedIDs = Array.from(allIDs).sort();
    
    for (const id of sortedIDs) {
        const data1Entry = data1ByID[id];
        const data2Entry = data2ByID[id];
        
        if (data1Entry && data2Entry) {
            // Both files have this ID - check for Account ID/Number mismatch
            const needsGap = checkNeedsAccountGap(id, data1Entry, data2Entry);
            
            if (needsGap) {
                // Insert gap rows first to show these rows are not related
                file1Rows.push({ 
                    row: [], 
                    id: `gap-before-${id}`, 
                    type: 'gap',
                    gapReason: 'Account ID/Number mismatch - rows not related'
                });
                file2Rows.push({ 
                    row: [], 
                    id: `gap-before-${id}`, 
                    type: 'gap',
                    gapReason: 'Account ID/Number mismatch - rows not related'
                });
            }
            
            // Add actual data rows
            file1Rows.push({
                row: data1Entry.row,
                id: id,
                type: 'data',
                hasAccountMismatch: needsGap,
                mismatchDetails: getMismatchDetailsForRow(id, results),
                rowIndex: file1Rows.length
            });
            
            file2Rows.push({
                row: data2Entry.row,
                id: id,
                type: 'data',
                hasAccountMismatch: needsGap,
                mismatchDetails: getMismatchDetailsForRow(id, results),
                rowIndex: file2Rows.length
            });
            
            mappings.push({ 
                file1Index: file1Rows.length - 1, 
                file2Index: file2Rows.length - 1, 
                id,
                hasAccountMismatch: needsGap
            });
            
        } else if (data1Entry) {
            // Only in file 1 - missing in file 2
            file1Rows.push({
                row: data1Entry.row,
                id: id,
                type: 'data',
                missing: 'file2',
                mismatchDetails: getMismatchDetailsForRow(id, results),
                rowIndex: file1Rows.length
            });
            
            file2Rows.push({
                row: [],
                id: `missing-${id}`,
                type: 'missing',
                missingFrom: 'file1',
                missingId: id,
                rowIndex: file2Rows.length
            });
            
        } else if (data2Entry) {
            // Only in file 2 - missing in file 1
            file1Rows.push({
                row: [],
                id: `missing-${id}`,
                type: 'missing',
                missingFrom: 'file2',
                missingId: id,
                rowIndex: file1Rows.length
            });
            
            file2Rows.push({
                row: data2Entry.row,
                id: id,
                type: 'data',
                missing: 'file1',
                mismatchDetails: getMismatchDetailsForRow(id, results),
                rowIndex: file2Rows.length
            });
        }
    }
    
    return { file1Rows, file2Rows, mappings };
}

function checkNeedsAccountGap(id, data1Entry, data2Entry) {
    if (!data1Entry || !data2Entry) return false;
    
    // Check Account ID mismatch
    if (sideBySideData.accountIdColumn) {
        const { file1Index, file2Index } = sideBySideData.accountIdColumn;
        const val1 = data1Entry.row[file1Index] || '';
        const val2 = data2Entry.row[file2Index] || '';
        if (String(val1).trim() !== String(val2).trim()) {
            return true;
        }
    }
    
    // Check Account Number mismatch
    if (sideBySideData.accountNumberColumn) {
        const { file1Index, file2Index } = sideBySideData.accountNumberColumn;
        const val1 = data1Entry.row[file1Index] || '';
        const val2 = data2Entry.row[file2Index] || '';
        if (String(val1).trim() !== String(val2).trim()) {
            return true;
        }
    }
    
    return false;
}

function getMismatchDetailsForRow(id, results) {
    if (!results || !results.allData) return null;
    
    const rowResult = results.allData.find(row => row.idValue === id);
    return rowResult ? rowResult.columnDetails : null;
}

function renderSideBySideHeaders() {
    const data1Headers = sideBySideData.data1Headers;
    const data2Headers = sideBySideData.data2Headers;
    
    // Apply same column filtering logic as in renderJointSideBySideData
    let filteredData1Headers = data1Headers;
    let filteredData2Headers = data2Headers;
    
    if (window.selectedColumnFilter && window.selectedColumnFilter.length > 0) {
        // Filter File 1 headers
        filteredData1Headers = [];
        window.selectedColumnFilter.forEach(mapping => {
            const file1Index = data1Headers.findIndex(h => h === mapping.file1Column);
            if (file1Index >= 0) {
                filteredData1Headers.push(data1Headers[file1Index]);
            }
        });
        
        // Filter File 2 headers
        filteredData2Headers = [];
        window.selectedColumnFilter.forEach(mapping => {
            const file2Index = data2Headers.findIndex(h => h === mapping.file2Column);
            if (file2Index >= 0) {
                filteredData2Headers.push(data2Headers[file2Index]);
            }
        });
    }
    
    // Use File 1 container for the joint view and hide File 2 container
    const file1HeadersContainer = document.getElementById('file1Headers');
    const file2HeadersContainer = document.getElementById('file2Headers');
    
    if (file2HeadersContainer) {
        file2HeadersContainer.style.display = 'none';
    }
    
    if (file1HeadersContainer) {
        const singlePair = window.selectedColumnFilter && window.selectedColumnFilter.length === 1;
        let headerHTML = '<table class="comparison-table"><thead><tr>';
        
        if (singlePair) {
            headerHTML += `
                <th class="px-1 py-1 text-left text-xs font-semibold text-cyan-200 uppercase tracking-tight border-b border-cyan-400/50 joint-view-header-file1">
                    <div class="flex flex-col items-center space-y-0">
                        <span class="text-cyan-400 text-xs">📄</span>
                        <span class="font-medium text-xs text-center leading-tight">File 1</span>
                    </div>
                </th>
                <th class="px-1 py-1 text-left text-xs font-semibold text-green-200 uppercase tracking-tight border-b border-green-400/50 joint-view-header-file2">
                    <div class="flex flex-col items-center space-y-0">
                        <span class="text-green-400 text-xs">📋</span>
                        <span class="font-medium text-xs text-center leading-tight">File 2</span>
                    </div>
                </th>
            `;
        } else {
            // Add filtered File 1 headers grouped together
            filteredData1Headers.forEach((header, index) => {
                headerHTML += `
                    <th class="px-1 py-1 text-left text-xs font-semibold text-cyan-200 uppercase tracking-tight border-b border-cyan-400/50 joint-view-header-file1">
                        <div class="flex flex-col items-center space-y-0">
                            <span class="text-cyan-400 text-xs">📄</span>
                            <span class="font-medium text-xs text-center leading-tight">${escapeHtml(header || `Col${index + 1}`)}</span>
                        </div>
                    </th>
                `;
            });
            
            // Add filtered File 2 headers grouped together
            filteredData2Headers.forEach((header, index) => {
                headerHTML += `
                    <th class="px-1 py-1 text-left text-xs font-semibold text-green-200 uppercase tracking-tight border-b border-green-400/50 joint-view-header-file2">
                        <div class="flex flex-col items-center space-y-0">
                            <span class="text-green-400 text-xs">📋</span>
                            <span class="font-medium text-xs text-center leading-tight">${escapeHtml(header || `Col${index + 1}`)}</span>
                        </div>
                    </th>
                `;
            });
        }
        
        headerHTML += '</tr></thead></table>';
        file1HeadersContainer.innerHTML = headerHTML;
    }
}

function renderSideBySideData() {
    renderJointSideBySideData();
}

// Helper function to determine appropriate column width based on column type
function getColumnWidth(headerName, colIndex) {
    const header = headerName.toLowerCase();
    
    if (header.includes('account') && (header.includes('number') || header.includes('id'))) {
        return '140px'; // Account Number/ID - compact but readable
    } else if (header.includes('name') || header.includes('holder')) {
        return '120px'; // Names - compact
    } else if (header.includes('date') || header.includes('time')) {
        return '90px'; // Dates - compact
    } else if (header.includes('status')) {
        return '80px'; // Status - very compact
    } else if (header.includes('balance') || header.includes('amount')) {
        return '90px'; // Amounts - compact
    } else {
        return '100px'; // Default width for other columns
    }
}

// Helper function to check if a column should be compared between files
function isComparisonColumn(headerName) {
    const header = headerName.toLowerCase();
    return header.includes('last update date') || 
           header.includes('last transaction status') || 
           header.includes('closing balance') ||
           header.includes('date') ||
           header.includes('status') ||
           header.includes('balance');
}

// Helper function to find corresponding column index in the other file
// Prefer mapping-based correspondence; fall back to heuristics
function getMappedCorrespondingIndex(sourceHeader, sourceHeaders, targetHeaders) {
    try {
        const mappings = parsedDataContent && parsedDataContent.columnMappings ? parsedDataContent.columnMappings : null;
        const norm = (s) => String(s || '').trim().toLowerCase();
        if (mappings) {
            // file1 -> file2
            for (const [f1, f2] of Object.entries(mappings)) {
                if (norm(sourceHeader) === norm(f1)) {
                    const idx = targetHeaders.findIndex(h => norm(h) === norm(f2));
                    if (idx >= 0) return idx;
                }
                // Also allow reverse matching (file2 -> file1)
                if (norm(sourceHeader) === norm(f2)) {
                    const idx = targetHeaders.findIndex(h => norm(h) === norm(f1));
                    if (idx >= 0) return idx;
                }
            }
        }
    } catch (e) {
        // ignore and fall back
    }
    return findCorrespondingColumnIndexHeuristic(sourceHeader, sourceHeaders, targetHeaders);
}

function findCorrespondingColumnIndexHeuristic(sourceHeader, sourceHeaders, targetHeaders) {
    const sourceHeaderLower = sourceHeader.toLowerCase();
    
    // Look for exact match first
    let targetIndex = targetHeaders.findIndex(header => 
        header.toLowerCase() === sourceHeaderLower
    );
    
    if (targetIndex >= 0) return targetIndex;
    
    // Look for partial matches for common column types
    if (sourceHeaderLower.includes('last update') && sourceHeaderLower.includes('date')) {
        targetIndex = targetHeaders.findIndex(header => 
            header.toLowerCase().includes('last update') && header.toLowerCase().includes('date')
        );
    } else if (sourceHeaderLower.includes('last transaction') && sourceHeaderLower.includes('status')) {
        targetIndex = targetHeaders.findIndex(header => 
            header.toLowerCase().includes('last transaction') && header.toLowerCase().includes('status')
        );
    } else if (sourceHeaderLower.includes('closing') && sourceHeaderLower.includes('balance')) {
        targetIndex = targetHeaders.findIndex(header => 
            header.toLowerCase().includes('closing') && header.toLowerCase().includes('balance')
        );
    } else if (sourceHeaderLower.includes('date')) {
        targetIndex = targetHeaders.findIndex(header => 
            header.toLowerCase().includes('date')
        );
    } else if (sourceHeaderLower.includes('status')) {
        targetIndex = targetHeaders.findIndex(header => 
            header.toLowerCase().includes('status')
        );
    } else if (sourceHeaderLower.includes('balance')) {
        targetIndex = targetHeaders.findIndex(header => 
            header.toLowerCase().includes('balance')
        );
    }
    
    return targetIndex;
}

function isColumnMapped(header) {
    try {
        const mappings = parsedDataContent && parsedDataContent.columnMappings ? parsedDataContent.columnMappings : null;
        if (!mappings) return false;
        const norm = (s) => String(s || '').trim().toLowerCase();
        for (const [f1, f2] of Object.entries(mappings)) {
            if (norm(header) === norm(f1) || norm(header) === norm(f2)) return true;
        }
        return false;
    } catch (_) {
        return false;
    }
}

function normalizeForCompare(val, ignoreSpaces, caseInsensitive) {
    let v = String(val ?? '');
    if (ignoreSpaces) v = v.replace(/\s+/g, '');
    if (caseInsensitive) v = v.toLowerCase();
    return v.trim();
}


function renderJointSideBySideData() {
    const file1DataContainer = document.getElementById('file1Data');
    const file2DataContainer = document.getElementById('file2Data');
    
    if (!file1DataContainer || !file2DataContainer) return;
    
    // Hide File 2 container since we're using File 1 for joint display
    file2DataContainer.style.display = 'none';
    
    const data1Headers = sideBySideData.data1Headers;
    const data2Headers = sideBySideData.data2Headers;
    
    // Apply column filtering if active
    let filteredData1Headers = data1Headers;
    let filteredData2Headers = data2Headers;
    let filteredData1Indices = [];
    let filteredData2Indices = [];
    
    if (window.selectedColumnFilter && window.selectedColumnFilter.length > 0) {
        console.log('🔍 Applying column filter to Joint File Comparison');
        
        // Filter File 1 headers
        filteredData1Headers = [];
        filteredData1Indices = [];
        window.selectedColumnFilter.forEach(mapping => {
            const file1Index = data1Headers.findIndex(h => h === mapping.file1Column);
            if (file1Index >= 0) {
                filteredData1Headers.push(data1Headers[file1Index]);
                filteredData1Indices.push(file1Index);
            }
        });
        
        // Filter File 2 headers
        filteredData2Headers = [];
        filteredData2Indices = [];
        window.selectedColumnFilter.forEach(mapping => {
            const file2Index = data2Headers.findIndex(h => h === mapping.file2Column);
            if (file2Index >= 0) {
                filteredData2Headers.push(data2Headers[file2Index]);
                filteredData2Indices.push(file2Index);
            }
        });
        
        console.log(`📊 Filtered to ${filteredData1Headers.length + filteredData2Headers.length} columns:`, 
                   [...filteredData1Headers, ...filteredData2Headers]);
    } else {
        // Show all columns
        filteredData1Indices = data1Headers.map((_, index) => index);
        filteredData2Indices = data2Headers.map((_, index) => index);
        console.log('📊 Showing all columns in Joint File Comparison');
    }
    
    // Create a mapping of all Account IDs to their data for proper matching
    const allRowsMap = new Map();
    
    // Helper function to get Account ID/Number from row data (mapped or by header)
    const getAccountIdFromRow = (rowData, headers) => {
        if (!rowData || !rowData.row) return null;
        // Prefer mapped Account ID
        if (sideBySideData.accountIdColumn) {
            const idx = headers === data1Headers ? sideBySideData.accountIdColumn.file1Index : sideBySideData.accountIdColumn.file2Index;
            if (typeof idx === 'number' && idx >= 0) {
                const v = rowData.row[idx];
                if (v !== undefined && String(v).trim() !== '') return String(v).trim();
            }
        }
        // Fall back to mapped Account Number
        if (sideBySideData.accountNumberColumn) {
            const idx = headers === data1Headers ? sideBySideData.accountNumberColumn.file1Index : sideBySideData.accountNumberColumn.file2Index;
            if (typeof idx === 'number' && idx >= 0) {
                const v = rowData.row[idx];
                if (v !== undefined && String(v).trim() !== '') return String(v).trim();
            }
        }
        // Try header name match
        let byNameIdx = headers.findIndex(h => h && h.toLowerCase().includes('account') && h.toLowerCase().includes('id'));
        if (byNameIdx < 0) {
            byNameIdx = headers.findIndex(h => h && h.toLowerCase().includes('account') && h.toLowerCase().includes('number'));
        }
        if (byNameIdx >= 0) {
            return String(rowData.row[byNameIdx] || '').trim();
        }
        // Final fallback
        return String(rowData.row[0] || '').trim();
    };
    
    // Process File 1 rows
    sideBySideData.file1Rows.forEach(rowData => {
        if (rowData.type === 'data') {
            const accountId = getAccountIdFromRow(rowData, data1Headers);
            if (accountId) {
                allRowsMap.set(accountId, {
                    accountId: accountId,
                    file1Data: rowData,
                    file2Data: null,
                    hasAccountMismatch: rowData.hasAccountMismatch
                });
                        }
                    }
                });
                
    // Process File 2 rows
    sideBySideData.file2Rows.forEach(rowData => {
        if (rowData.type === 'data') {
            const accountId = getAccountIdFromRow(rowData, data2Headers);
            if (accountId) {
                if (allRowsMap.has(accountId)) {
                    // Found matching Account ID, add File 2 data
                    allRowsMap.get(accountId).file2Data = rowData;
                } else {
                    // Account ID only exists in File 2
                    allRowsMap.set(accountId, {
                        accountId: accountId,
                        file1Data: null,
                        file2Data: rowData,
                        hasAccountMismatch: rowData.hasAccountMismatch
                    });
                }
            }
        }
    });
    
    // Sort rows by Account ID for proper matching
    const sortedRows = Array.from(allRowsMap.values()).sort((a, b) => {
        // Get Account ID/Number from both files for comparison using the same logic as above
        const getAccountId = (data, headers) => {
            if (!data || !data.row) return '';
            if (sideBySideData.accountIdColumn) {
                const idx = headers === data1Headers ? sideBySideData.accountIdColumn.file1Index : sideBySideData.accountIdColumn.file2Index;
                if (typeof idx === 'number' && idx >= 0) {
                    const v = data.row[idx];
                    if (v !== undefined && String(v).trim() !== '') return String(v).trim();
                }
            }
            if (sideBySideData.accountNumberColumn) {
                const idx = headers === data1Headers ? sideBySideData.accountNumberColumn.file1Index : sideBySideData.accountNumberColumn.file2Index;
                if (typeof idx === 'number' && idx >= 0) {
                    const v = data.row[idx];
                    if (v !== undefined && String(v).trim() !== '') return String(v).trim();
                }
            }
            let byNameIdx = headers.findIndex(h => h && h.toLowerCase().includes('account') && h.toLowerCase().includes('id'));
            if (byNameIdx < 0) {
                byNameIdx = headers.findIndex(h => h && h.toLowerCase().includes('account') && h.toLowerCase().includes('number'));
            }
            if (byNameIdx >= 0) {
                return String(data.row[byNameIdx] || '').trim();
            }
            return String(data.row[0] || '').trim();
        };

        
        const accountId1 = a.file1Data ? getAccountId(a.file1Data, data1Headers) : 
                          a.file2Data ? getAccountId(a.file2Data, data2Headers) : '';
        const accountId2 = b.file1Data ? getAccountId(b.file1Data, data1Headers) : 
                          b.file2Data ? getAccountId(b.file2Data, data2Headers) : '';
        
        // Sort by Account ID alphabetically
        return accountId1.localeCompare(accountId2);
    });
    
    let tableHTML = '<table class="comparison-table"><tbody class="divide-y divide-gray-700/50">';
    
    sortedRows.forEach((jointRow, rowIndex) => {
        const { accountId, file1Data, file2Data, hasAccountMismatch } = jointRow;
        
        // Check if we need a gap row for account mismatches
        if (hasAccountMismatch && file1Data && file2Data) {
            const totalColumns = 2; // Two-column view
            tableHTML += `
                <tr class="bg-yellow-900/20 border-y-2 border-yellow-500/50 joint-account-mismatch" style="height: 40px;">
                    <td colspan="${totalColumns}" class="px-3 py-3 text-center text-yellow-300 text-xs font-medium">
                        ⚠️ Account ID/Number mismatch for Account ID "${accountId}" - data may not be related
                    </td>
                </tr>
            `;
        }
        
        // Create the main data row
        const baseRowClass = rowIndex % 2 === 0 ? 'bg-gray-800/20' : 'bg-gray-800/5';
        const accountMismatchClass = hasAccountMismatch ? 'border-l-4 border-yellow-400' : '';
        
        tableHTML += `<tr class=\"hover:bg-gray-700/20 transition-all duration-200 ${baseRowClass} ${accountMismatchClass} joint-view\" style=\"height: 28px;\">`;
        
        const singlePair = window.selectedColumnFilter && window.selectedColumnFilter.length === 1;
        if (singlePair) {
            const pair = window.selectedColumnFilter[0];
            const idx1 = data1Headers.findIndex(h => h === pair.file1Column);
            const idx2 = data2Headers.findIndex(h => h === pair.file2Column);
            const v1 = file1Data ? (file1Data.row[idx1 >= 0 ? idx1 : 0] || '') : '';
            const v2 = file2Data ? (file2Data.row[idx2 >= 0 ? idx2 : 0] || '') : '';
            const ignoreSpaces = document.getElementById('ignoreSpaces')?.checked || false;
            const caseInsensitive = document.getElementById('caseInsensitive')?.checked || false;
            const n1 = normalizeForCompare(v1, ignoreSpaces, caseInsensitive);
            const n2 = normalizeForCompare(v2, ignoreSpaces, caseInsensitive);
            const mismatch = n1 !== '' && n2 !== '' && n1 !== n2;
            const c1 = mismatch ? 'joint-cell-mismatch-file1' : 'joint-view-file1-col';
            const c2 = mismatch ? 'joint-cell-mismatch-file2' : 'joint-view-file2-col';
            tableHTML += `
                <td class=\"${c1} professional-text\" style=\"min-width:${getColumnWidth(pair.file1Column || 'File 1', idx1)};\">\n                    <div class=\"overflow-hidden\" title=\"${escapeHtml(String(v1))}\">${escapeHtml(String(v1) || '-')}<\/div>\n                </td>
                <td class=\"${c2} professional-text\" style=\"min-width:${getColumnWidth(pair.file2Column || 'File 2', idx2)};\">\n                    <div class=\"overflow-hidden\" title=\"${escapeHtml(String(v2))}\">${escapeHtml(String(v2) || '-')}<\/div>\n                </td>`;
        } else {
            // Render filtered File 1 columns grouped together
            filteredData1Headers.forEach((header, headerIndex) => {
                const colIndex = filteredData1Indices[headerIndex];
                const file1Value = file1Data ? (file1Data.row[colIndex] || '') : '';
                // mismatch highlight against mapped counterpart
                let cellClass1 = 'joint-view-file1-col';
                (function(){
                    const targetIdx = getMappedCorrespondingIndex(header, data1Headers, data2Headers);
                    if (file2Data && targetIdx >= 0) {
                        const file2Value = file2Data.row[targetIdx] || '';
                        const ignoreSpaces = document.getElementById('ignoreSpaces')?.checked || false;
                        const caseInsensitive = document.getElementById('caseInsensitive')?.checked || false;
                        const n1 = normalizeForCompare(file1Value, ignoreSpaces, caseInsensitive);
                        const n2 = normalizeForCompare(file2Value, ignoreSpaces, caseInsensitive);
                        if (n1 !== '' && n2 !== '' && n1 !== n2) {
                            cellClass1 = 'joint-cell-mismatch-file1';
                        }
                    }
                })();
                tableHTML += `
                    <td class=\"${cellClass1} professional-text\" style=\"min-width: ${getColumnWidth(header, colIndex)};\">\n                        <div class=\"overflow-hidden\" title=\"${escapeHtml(String(file1Value))}\">\n                            ${escapeHtml(String(file1Value) || '-')}\n                        </div>\n                    </td>`;
            });
            
            // Then, render filtered File 2 columns grouped together
            filteredData2Headers.forEach((header, headerIndex) => {
                const colIndex = filteredData2Indices[headerIndex];
                const file2Value = file2Data ? (file2Data.row[colIndex] || '') : '';
                let cellClass2 = 'joint-view-file2-col';
                (function(){
                    const targetIdx = getMappedCorrespondingIndex(header, data2Headers, data1Headers);
                    if (file1Data && targetIdx >= 0) {
                        const file1Value = file1Data.row[targetIdx] || '';
                        const ignoreSpaces = document.getElementById('ignoreSpaces')?.checked || false;
                        const caseInsensitive = document.getElementById('caseInsensitive')?.checked || false;
                        const n1 = normalizeForCompare(file1Value, ignoreSpaces, caseInsensitive);
                        const n2 = normalizeForCompare(file2Value, ignoreSpaces, caseInsensitive);
                        if (n1 !== '' && n2 !== '' && n1 !== n2) {
                            cellClass2 = 'joint-cell-mismatch-file2';
                        }
                    }
                })();
                tableHTML += `
                    <td class=\"${cellClass2} professional-text\" style=\"min-width: ${getColumnWidth(header, colIndex)};\">\n                        <div class=\"overflow-hidden\" title=\"${escapeHtml(String(file2Value))}\">\n                            ${escapeHtml(String(file2Value) || '-')}\n                        </div>\n                    </td>`;
            });
        }

        tableHTML += '</tr>';
    });
    
    tableHTML += '</tbody></table>';
    file1DataContainer.innerHTML = tableHTML;
}

function renderCombinedData() {
    const file1DataContainer = document.getElementById('file1Data');
    const file2DataContainer = document.getElementById('file2Data');
    
    if (!file1DataContainer || !file2DataContainer) return;
    
    // Hide File 2 container
    file2DataContainer.style.display = 'none';
    
    const data1Headers = sideBySideData.data1Headers;
    const data2Headers = sideBySideData.data2Headers;
    const maxColumns = Math.max(data1Headers.length, data2Headers.length);
    
    // Combine all rows from both files
    const allRows = [];
    
    // Add File 1 rows
    sideBySideData.file1Rows.forEach((rowData, index) => {
        if (rowData.type === 'data') {
            allRows.push({
                ...rowData,
                source: 'File 1',
                sourceClass: 'border-l-4 border-cyan-500 bg-cyan-900/10',
                sourceIcon: '📄',
                sourceColor: 'text-cyan-400'
            });
        }
    });
    
    // Add File 2 rows
    sideBySideData.file2Rows.forEach((rowData, index) => {
        if (rowData.type === 'data') {
            allRows.push({
                ...rowData,
                source: 'File 2',
                sourceClass: 'border-l-4 border-green-500 bg-green-900/10',
                sourceIcon: '📋',
                sourceColor: 'text-green-400'
            });
        }
    });
    
    // Sort by ID for better comparison
    allRows.sort((a, b) => {
        const idA = String(a.id || '').toLowerCase();
        const idB = String(b.id || '').toLowerCase();
        return idA.localeCompare(idB);
    });
    
    const tableHTML = `
                            <table class="min-w-full">
                                <tbody class="divide-y divide-gray-700/50">
                ${allRows.map((rowData, rowIndex) => {
                                        const baseRowClass = rowIndex % 2 === 0 ? 'bg-gray-800/30' : 'bg-gray-800/10';
                    const accountMismatchClass = rowData.hasAccountMismatch ? 'border-r-4 border-yellow-500' : '';
                                        
                                        return `
                        <tr class="hover:bg-gray-700/20 transition-all duration-200 ${baseRowClass} ${rowData.sourceClass} ${accountMismatchClass}">
                            <td class="px-3 py-1 text-xs font-medium whitespace-nowrap w-20">
                                <div class="flex items-center space-x-1">
                                    <span class="${rowData.sourceColor}">${rowData.sourceIcon}</span>
                                    <span class="${rowData.sourceColor}">${rowData.source}</span>
                                </div>
                            </td>
                            ${Array.from({ length: maxColumns }, (_, colIndex) => {
                                const cellValue = rowData.row[colIndex] || '';
                                
                                // Determine which header to use for mismatch checking
                                const headerName = rowData.source === 'File 1' ? 
                                    data1Headers[colIndex] : 
                                    data2Headers[colIndex];
                                
                                const isMismatched = isCellMismatchedInRow(
                                    headerName, 
                                    cellValue, 
                                    rowData.mismatchDetails, 
                                    rowData.source === 'File 1'
                                );
                                
                                const highlightClass = isMismatched ? '' : '';
                                                    
                                                    return `
                                                        <td class="px-3 py-1 text-xs text-gray-300 whitespace-nowrap ${highlightClass}">
                                        <div class="max-w-32 overflow-hidden text-ellipsis" title="${escapeHtml(String(cellValue))}">
                                                                ${escapeHtml(String(cellValue) || '-')}
                                                            </div>
                                                        </td>
                                                    `;
                                                }).join('')}
                                            </tr>
                                        `;
                                    }).join('')}
                                </tbody>
                            </table>
    `;
    
    file1DataContainer.innerHTML = tableHTML;
}

function renderFile1Data() {
    const file1DataContainer = document.getElementById('file1Data');
    if (!file1DataContainer) return;
    
    const headers = sideBySideData.data1Headers;
    
    const tableHTML = `
                            <table class="min-w-full">
                                <tbody class="divide-y divide-gray-700/50">
                ${sideBySideData.file1Rows.map((rowData, rowIndex) => {
                    if (rowData.type === 'gap') {
                        return `
                            <tr class="bg-yellow-900/20 border-y-2 border-yellow-500/50" style="height: 40px;">
                                <td colspan="${headers.length}" class="px-3 py-3 text-center text-yellow-300 text-xs font-medium">
                                    ⚠️ ${rowData.gapReason || 'Account ID/Number mismatch - rows not related'}
                                </td>
                            </tr>
                        `;
                    } else if (rowData.type === 'missing') {
                        return `
                            <tr class="bg-red-900/30 border-l-4 border-red-500" style="height: 32px;">
                                <td colspan="${headers.length}" class="px-3 py-2 text-center text-red-300 text-xs font-medium">
                                    🚫 Row "${rowData.missingId || 'Unknown'}" missing in File 2
                                </td>
                            </tr>
                        `;
                    } else {
                        // Regular data row
                        const baseRowClass = rowIndex % 2 === 0 ? 'bg-gray-800/20' : 'bg-gray-800/5';
                        const accountMismatchClass = rowData.hasAccountMismatch ? 'border-l-4 border-yellow-400' : '';
                                        
                                        return `
                            <tr class="hover:bg-cyan-800/20 transition-all duration-200 ${baseRowClass} ${accountMismatchClass}" style="height: 32px;">
                                ${headers.map((header, colIndex) => {
                                    const cellValue = rowData.row[colIndex] || '';
                                    const isMismatched = isCellMismatchedInRow(header, cellValue, rowData.mismatchDetails, true);
                                    const highlightClass = isMismatched ? 'bg-transparent' : 'bg-transparent';
                                    const textClass = isMismatched ? 'text-gray-300 font-medium' : 'text-gray-300';
                                                    
                                                    return `
                                        <td class="px-3 py-2 text-xs whitespace-nowrap ${highlightClass} ${textClass} transition-colors duration-200">
                                            <div class="max-w-28 overflow-hidden text-ellipsis" title="${escapeHtml(String(cellValue))}">
                                                                ${escapeHtml(String(cellValue) || '-')}
                                                            </div>
                                                        </td>
                                                    `;
                                                }).join('')}
                                            </tr>
                                        `;
                    }
                                    }).join('')}
                                </tbody>
                            </table>
    `;
    
    file1DataContainer.innerHTML = tableHTML;
}

function renderFile2Data() {
    const file2DataContainer = document.getElementById('file2Data');
    if (!file2DataContainer) return;
    
    // Make sure File 2 container is visible
    file2DataContainer.style.display = 'block';
    
    const headers = sideBySideData.data2Headers;
    
    const tableHTML = `
        <table class="min-w-full">
            <tbody class="divide-y divide-gray-700/50">
                ${sideBySideData.file2Rows.map((rowData, rowIndex) => {
                    if (rowData.type === 'gap') {
                        return `
                            <tr class="bg-yellow-900/20 border-y-2 border-yellow-500/50" style="height: 40px;">
                                <td colspan="${headers.length}" class="px-3 py-3 text-center text-yellow-300 text-xs font-medium">
                                    ⚠️ ${rowData.gapReason || 'Account ID/Number mismatch - rows not related'}
            </td>
                            </tr>
                        `;
                    } else if (rowData.type === 'missing') {
                        return `
                            <tr class="bg-red-900/30 border-l-4 border-red-500" style="height: 32px;">
                                <td colspan="${headers.length}" class="px-3 py-2 text-center text-red-300 text-xs font-medium">
                                    🚫 Row "${rowData.missingId || 'Unknown'}" missing in File 1
                                </td>
                            </tr>
                        `;
                    } else {
                        // Regular data row
                        const baseRowClass = rowIndex % 2 === 0 ? 'bg-gray-800/20' : 'bg-gray-800/5';
                        const accountMismatchClass = rowData.hasAccountMismatch ? 'border-l-4 border-yellow-400' : '';
                        
                        return `
                            <tr class="hover:bg-green-800/20 transition-all duration-200 ${baseRowClass} ${accountMismatchClass}" style="height: 32px;">
                                ${headers.map((header, colIndex) => {
                                    const cellValue = rowData.row[colIndex] || '';
                                    const isMismatched = isCellMismatchedInRow(header, cellValue, rowData.mismatchDetails, false);
                                    const highlightClass = isMismatched ? 'bg-transparent' : 'bg-transparent';
                                    const textClass = isMismatched ? 'text-gray-300 font-medium' : 'text-gray-300';
                                    
                                    return `
                                        <td class="px-3 py-2 text-xs whitespace-nowrap ${highlightClass} ${textClass} transition-colors duration-200">
                                            <div class="max-w-28 overflow-hidden text-ellipsis" title="${escapeHtml(String(cellValue))}">
                                                ${escapeHtml(String(cellValue) || '-')}
                                            </div>
                                        </td>
                                    `;
                                }).join('')}
                            </tr>
                        `;
                    }
                }).join('')}
            </tbody>
        </table>
    `;
    
    file2DataContainer.innerHTML = tableHTML;
}

function isCellMismatchedInRow(columnHeader, cellValue, mismatchDetails, isFile1) {
    if (!mismatchDetails || !Array.isArray(mismatchDetails)) return false;
    
    return mismatchDetails.some(detail => {
        const targetColumn = isFile1 ? detail.file1Column : detail.file2Column;
        const targetValue = isFile1 ? detail.file1Value : detail.file2Value;
        
        // Check if this column matches and the status is mismatch
        const columnMatches = (targetColumn === columnHeader || targetColumn === columnHeader.trim());
        const isMismatch = detail.status === 'Mismatch';
        
        // Additional check: ensure the cell value matches what's expected
        const valueMatches = String(targetValue || '').trim() === String(cellValue || '').trim();
        
        return columnMatches && isMismatch && valueMatches;
    });
}

function initializeScrollSync() {
    const file1ScrollArea = document.getElementById('file1ScrollArea');
    const file2ScrollArea = document.getElementById('file2ScrollArea');
    
    if (!file1ScrollArea || !file2ScrollArea) {
        console.warn('Scroll areas not found for synchronization');
            return;
        }
        
    let isFile1Scrolling = false;
    let isFile2Scrolling = false;
    let syncTimeout;
    
    // Remove any existing listeners
    file1ScrollArea.removeEventListener('scroll', file1ScrollArea._scrollHandler);
    file2ScrollArea.removeEventListener('scroll', file2ScrollArea._scrollHandler);
    
    // File 1 scroll handler
    file1ScrollArea._scrollHandler = () => {
        if (!scrollSyncEnabled || isFile2Scrolling) return;
        
        clearTimeout(syncTimeout);
        isFile1Scrolling = true;
        
        // Sync both vertical and horizontal scroll
        file2ScrollArea.scrollTop = file1ScrollArea.scrollTop;
        file2ScrollArea.scrollLeft = file1ScrollArea.scrollLeft;
        
        syncTimeout = setTimeout(() => { 
            isFile1Scrolling = false; 
        }, 100);
    };
    
    // File 2 scroll handler
    file2ScrollArea._scrollHandler = () => {
        if (!scrollSyncEnabled || isFile1Scrolling) return;
        
        clearTimeout(syncTimeout);
        isFile2Scrolling = true;
        
        // Sync both vertical and horizontal scroll
        file1ScrollArea.scrollTop = file2ScrollArea.scrollTop;
        file1ScrollArea.scrollLeft = file2ScrollArea.scrollLeft;
        
        syncTimeout = setTimeout(() => { 
            isFile2Scrolling = false; 
        }, 100);
    };
    
    // Add event listeners
    file1ScrollArea.addEventListener('scroll', file1ScrollArea._scrollHandler, { passive: true });
    file2ScrollArea.addEventListener('scroll', file2ScrollArea._scrollHandler, { passive: true });
    
    console.log('📊 Scroll synchronization initialized');
}

function updateComparisonStats() {
    const file1Stats = document.getElementById('file1Stats');
    const file2Stats = document.getElementById('file2Stats');
    const comparisonStats = document.getElementById('comparisonStats');
    
    const file1Count = sideBySideData.file1Rows.filter(r => r.type === 'data').length;
    const file2Count = sideBySideData.file2Rows.filter(r => r.type === 'data').length;
    const gapCount = sideBySideData.file1Rows.filter(r => r.type === 'gap').length;
    const missingCount = sideBySideData.file1Rows.filter(r => r.type === 'missing').length + 
                        sideBySideData.file2Rows.filter(r => r.type === 'data' && r.missing === 'file1').length;
    
    // Get unique IDs for total joint records
    const allIDs = new Set();
    sideBySideData.file1Rows.forEach(r => r.type === 'data' && r.id && allIDs.add(r.id));
    sideBySideData.file2Rows.forEach(r => r.type === 'data' && r.id && allIDs.add(r.id));
    const totalJointRecords = allIDs.size;
    
    // Calculate visual match statistics based on red highlighting
    const { matchedRows, mismatchedRows } = calculateVisualMatchStatistics();
    
    // Update the main comparison summary
    updateMainComparisonSummary(totalJointRecords, matchedRows, mismatchedRows);
    
    if (file1Stats) {
        file1Stats.textContent = `📄 File 1: ${file1Count} records | 📋 File 2: ${file2Count} records | 🔗 Joint: ${totalJointRecords} records`;
    }
    
    if (file2Stats) {
        // Hide file2Stats in joint view
        const file2StatsContainer = file2Stats.closest('.bg-green-900\\/20, .bg-green-900\\/10');
        if (file2StatsContainer) {
            file2StatsContainer.style.display = 'none';
        }
    }
    
    if (comparisonStats) {
        comparisonStats.textContent = `📊 Sorted by Account ID • ${gapCount} account mismatches • ${missingCount} missing records`;
    }
}

function calculateVisualMatchStatistics() {
    let matchedRows = 0;
    let mismatchedRows = 0;
    
    console.log('🔍 Calculating visual match statistics...');
    
    // Use the actual rendered table to check for red highlighting
    const mainTable = document.querySelector('#file1Data table tbody, .comparison-table tbody');
    if (!mainTable) {
        console.error('❌ No main comparison table found for visual analysis');
        return { matchedRows: 0, mismatchedRows: 0 };
    }
    
    const tableRows = mainTable.querySelectorAll('tr');
    console.log(`📊 Found ${tableRows.length} rows in the comparison table`);
    
    let processedDataRows = 0;
    
    tableRows.forEach((row, index) => {
        // Skip gap rows and header rows
        if (row.classList.contains('joint-account-mismatch') || 
            row.querySelector('th') || 
            row.textContent.includes('Account ID/Number mismatch')) {
            return;
        }
        
        processedDataRows++;
        
        // Check if this row has ANY red highlighted cells
        const hasRedCells = checkRowForRedHighlighting(row);
        
        if (hasRedCells) {
            mismatchedRows++;
            if (processedDataRows <= 5) {
                console.log(`❌ Row ${processedDataRows}: MISMATCHED (has red cells)`);
            }
        } else {
            matchedRows++;
            if (processedDataRows <= 5) {
                console.log(`✅ Row ${processedDataRows}: MATCHED (no red cells)`);
            }
        }
    });
    
    console.log(`📈 Visual analysis complete:`);
    console.log(`- Total data rows processed: ${processedDataRows}`);
    console.log(`- Matched rows: ${matchedRows}`);
    console.log(`- Mismatched rows: ${mismatchedRows}`);
    
    return { matchedRows, mismatchedRows };
}

// Helper function to check if a row has red highlighting
function checkRowForRedHighlighting(row) {
    const cells = row.querySelectorAll('td');
    
    for (let cell of cells) {
        // Check for mismatch indicators (no color highlighting, just class-based detection)
        const hasMismatchIndicator = (
            cell.classList.contains('joint-cell-mismatch-file1') ||
            cell.classList.contains('joint-cell-mismatch-file2')
        );
        
        // Check if cell has mismatch class indicator
        if (hasMismatchIndicator) {
            return true;
        }
    }
    
    return false;
}

function updateMainComparisonSummary(totalRows, matchedRows, mismatchedRows) {
    console.log(`Updating comparison summary: ${totalRows} total, ${matchedRows} matched, ${mismatchedRows} mismatched`);
    
    // Calculate accuracy percentage
    const accuracy = totalRows > 0 ? ((matchedRows / totalRows) * 100).toFixed(1) : 0;
    
    // Update all summary elements
    const totalRowsElement = document.getElementById('totalRows');
    if (totalRowsElement) {
        totalRowsElement.textContent = totalRows;
    }
    
    const matchedRowsElement = document.getElementById('matchedRows');
    if (matchedRowsElement) {
        matchedRowsElement.textContent = matchedRows;
    }
    
    const mismatchedRowsElement = document.getElementById('mismatchedRows');
    if (mismatchedRowsElement) {
        mismatchedRowsElement.textContent = mismatchedRows;
    }
    
    const accuracyElement = document.getElementById('accuracyPercentage');
    if (accuracyElement) {
        accuracyElement.textContent = `${accuracy}%`;
    }
    
    // Show the summary section
    const summarySection = document.getElementById('summarySection');
    if (summarySection) {
        summarySection.classList.remove('hidden');
    }
    
    console.log(`✅ Summary updated: Total=${totalRows}, Matched=${matchedRows}, Mismatched=${mismatchedRows}, Accuracy=${accuracy}%`);
}

// Control functions
function toggleScrollSync() {
    scrollSyncEnabled = !scrollSyncEnabled;
    const button = document.getElementById('syncScrollBtn');
    if (button) {
        button.textContent = scrollSyncEnabled ? '🔗 Sync Scrolling' : '🔓 Sync Disabled';
        button.className = scrollSyncEnabled ? 
            'px-3 py-1 bg-blue-600 hover:bg-blue-700 text-white text-sm rounded transition-colors' :
            'px-3 py-1 bg-gray-600 hover:bg-gray-700 text-white text-sm rounded transition-colors';
    }
}

function toggleShowAllRows() {
    showAllRowsMode = !showAllRowsMode;
    const button = document.getElementById('showAllRowsBtn');
    if (button) {
        button.textContent = showAllRowsMode ? '🔍 Show Mismatched Only' : '👁️ Show All Rows';
    }
    
    // Re-render the data with the new filter
    renderSideBySideData();
}

// Utility function for HTML escaping
function escapeHtml(text) {
    const div = document.createElement('div');
    div.textContent = text;
    return div.innerHTML;
}

async function performComparison() {
    console.log('Starting comparison...');
    showLoading(true);
    
    try {
        // Validate all files are uploaded
        if (!uploadedFiles.schema1 || !uploadedFiles.schema2 || !uploadedFiles.data1 || !uploadedFiles.data2 || !uploadedFiles.mapping) {
            showError('Please upload all required files before comparing.');
            return;
        }

        // Get configuration options
        const ignoreSpaces = document.getElementById('ignoreSpaces')?.checked || false;
        const caseInsensitive = document.getElementById('caseInsensitive')?.checked || false;
        
        // Parse all files
        console.log('Parsing files...');
        const schema1Data = await parseExcelFile(uploadedFiles.schema1);
        const schema2Data = await parseExcelFile(uploadedFiles.schema2);
        const data1Content = await parseDataFile(uploadedFiles.data1);
        const data2Content = await parseDataFile(uploadedFiles.data2);
        const mappingData = await parseExcelFile(uploadedFiles.mapping);
        
        // Validate parsed data
        if (!data1Content || !data1Content.length) {
            throw new Error('Data File 1 could not be parsed or is empty');
        }
        if (!data2Content || !data2Content.length) {
            throw new Error('Data File 2 could not be parsed or is empty');
        }
        if (!mappingData || !mappingData.length) {
            throw new Error('Mapping file could not be parsed or is empty');
        }
        
        console.log('Files parsed successfully');
        console.log('Data File 1 rows:', data1Content.length);
        console.log('Data File 2 rows:', data2Content.length);
        console.log('Mapping data rows:', mappingData.length);
        
        // Store parsed data content globally for display
        parsedDataContent.data1 = data1Content;
        parsedDataContent.data2 = data2Content;
        
        // Extract column metadata from schemas
        const schema1Columns = extractColumnMetadata(schema1Data);
        const schema2Columns = extractColumnMetadata(schema2Data);
        
        console.log('Schema 1 columns extracted:', schema1Columns);
        console.log('Schema 2 columns extracted:', schema2Columns);
        
        // Prefer schema headers for display only when their count matches the data columns
        const schema1Headers = Object.keys(schema1Columns);
        const schema2Headers = Object.keys(schema2Columns);
        
        console.log('Schema 1 headers:', schema1Headers);
        console.log('Schema 2 headers:', schema2Headers);
        
        // Set display-only overrides; do not mutate original data headers
        if (schema1Headers.length && data1Content[0] && schema1Headers.length === data1Content[0].length) {
            parsedDataContent.overrideHeaders1 = schema1Headers;
        } else {
            parsedDataContent.overrideHeaders1 = null;
        }
        if (schema2Headers.length && data2Content[0] && schema2Headers.length === data2Content[0].length) {
            parsedDataContent.overrideHeaders2 = schema2Headers;
        } else {
            parsedDataContent.overrideHeaders2 = null;
        }
        
        // Process mapping file
        console.log('Processing mapping file...');
        const columnMappings = processMappingFile(mappingData);
        
        if (!columnMappings || Object.keys(columnMappings).length === 0) {
            throw new Error('No valid column mappings found in mapping file.');
        }
        
        console.log('Column mappings processed:', columnMappings);
        
        // Store mapping data globally
        parsedDataContent.mappingData = mappingData;
        parsedDataContent.columnMappings = columnMappings;
        
        // Create mappingArray for intermediate comparison
        parsedDataContent.mappingArray = Object.entries(columnMappings).map(([file1Column, file2Column]) => ({
            file1Column: file1Column,
            file2Column: file2Column
        }));
        
        console.log('Mapping array created for intermediate comparison:', parsedDataContent.mappingArray);
        
        // Compare data files
        const results = compareDataFiles(
            data1Content,
            data2Content,
            columnMappings,
            ignoreSpaces,
            caseInsensitive
        );
        
        // Store results
        comparisonResults = results;
        
        console.log('Comparison results:', results);
        
        // Display results
        displayResults(results);
        // After results are visible, try auto-selecting preferred intermediate columns
        setTimeout(autoSelectPreferredIntermediateColumns, 500);
        
    } catch (error) {
        console.error('Comparison error:', error);
        showError(`Error during comparison: ${error.message}`);
    } finally {
        showLoading(false);
    }
}

function compareDataFiles(data1, data2, mappings, ignoreSpaces, caseInsensitive) {
    console.log('Comparing data files...');
    
    const results = {
            totalRows: 0,
            matchedRows: 0,
            mismatchedRows: 0,
            mismatches: [],
        allData: [],
        swappedRows: [],
        columnMappings: mappings
    };
    
    if (!data1.length || !data2.length) {
        return results;
    }
    
    // Get actual headers from data files
    const data1Headers = data1[0];
    const data2Headers = data2[0];
    
    // Create column index mappings
    const data1ColumnIndexes = {};
    const data2ColumnIndexes = {};
    
    data1Headers.forEach((header, index) => {
        data1ColumnIndexes[header.trim()] = index;
    });
    
    data2Headers.forEach((header, index) => {
        data2ColumnIndexes[header.trim()] = index;
    });
    
    // Process rows and find matches
    const comparedRows = [];
    const processedIds = new Set();
    
    // Process data1 rows and find their matches in data2
    for (let i = 1; i < data1.length; i++) {
        const row1 = data1[i];
        const idValue = row1[0] ? String(row1[0]).trim() : '';
        
        if (!idValue || processedIds.has(idValue)) continue;
        processedIds.add(idValue);
        
        // Find matching row in data2
        let matchingRow2 = null;
        
        for (let j = 1; j < data2.length; j++) {
            const row2 = data2[j];
            const idValue2 = row2[0] ? String(row2[0]).trim() : '';
            
            if (idValue === idValue2) {
                matchingRow2 = row2;
                break;
            }
        }
        
        // Create comparison data for this row pair
        let rowMatches = true;
        const columnDetails = [];
        const rowMismatches = [];
        
        if (matchingRow2) {
            // Compare mapped columns
            let validMappingsCount = 0;
            let matchedColumns = 0;
            
            Object.keys(mappings).forEach(file1Column => {
                const file2Column = mappings[file1Column];
                
                const file1Index = data1ColumnIndexes[file1Column];
                const file2Index = data2ColumnIndexes[file2Column];
                
                let file1Value = 'COLUMN NOT FOUND';
                let file2Value = 'COLUMN NOT FOUND';
                let columnMatch = true;
                let columnStatus = 'Match';
                
                if (file1Index !== undefined) {
                    file1Value = row1[file1Index] || '';
                } else {
                    columnMatch = false;
                    columnStatus = 'Column Not Found in Data File 1';
                }
                
                if (file2Index !== undefined) {
                    file2Value = matchingRow2[file2Index] || '';
                } else {
                    columnMatch = false;
                    columnStatus = 'Column Not Found in Data File 2';
                }
                
                if (file1Index !== undefined && file2Index !== undefined) {
                    validMappingsCount++;
                    const normalizedValue1 = normalizeValue(file1Value, ignoreSpaces, caseInsensitive);
                    const normalizedValue2 = normalizeValue(file2Value, ignoreSpaces, caseInsensitive);
                    
                    if (normalizedValue1 !== normalizedValue2) {
                        columnMatch = false;
                        columnStatus = 'Mismatch';
                        
                    rowMismatches.push({
                            column: file1Column,
                            file1Value,
                            file2Value,
                            file1Column,
                            file2Column
                        });
                    } else {
                        matchedColumns++;
                    }
                }
                
                columnDetails.push({
                    mappedColumnName: `${file1Column} ↔ ${file2Column}`,
                    file1Column,
                    file2Column,
                    file1Value,
                    file2Value,
                    match: columnMatch,
                    status: columnStatus
                });
            });
            
            rowMatches = (matchedColumns === validMappingsCount && rowMismatches.length === 0);
            
            comparedRows.push({
                rowNumber: comparedRows.length + 1,
                idValue: idValue,
                keyValue: idValue,
                matches: rowMatches,
                status: rowMatches ? 'Match' : 'Mismatch',
                columnDetails: columnDetails,
                mismatches: rowMismatches
            });
            
        } else {
            // Row exists in data1 but not in data2
            Object.keys(mappings).forEach(file1Column => {
                const file2Column = mappings[file1Column];
                const file1Index = data1ColumnIndexes[file1Column];
                let file1Value = 'COLUMN NOT FOUND';
                
                if (file1Index !== undefined) {
                    file1Value = row1[file1Index] || '';
                }
                
                columnDetails.push({
                    mappedColumnName: `${file1Column} ↔ ${file2Column}`,
                    file1Column,
                    file2Column,
                    file1Value,
                    file2Value: 'ROW NOT FOUND',
                    match: false,
                    status: 'Missing in Data File 2'
                });
            });
            
            comparedRows.push({
                rowNumber: comparedRows.length + 1,
                idValue: idValue,
                keyValue: idValue,
                matches: false,
                status: 'Missing in Data File 2',
                columnDetails: columnDetails,
                mismatches: []
            });
        }
    }
    
    // Check for rows that exist in data2 but not in data1
    for (let j = 1; j < data2.length; j++) {
        const row2 = data2[j];
        const idValue2 = row2[0] ? String(row2[0]).trim() : '';
        
        if (!idValue2 || processedIds.has(idValue2)) continue;
        processedIds.add(idValue2);
        
        const columnDetails = [];
        Object.keys(mappings).forEach(file1Column => {
            const file2Column = mappings[file1Column];
            const file2Index = data2ColumnIndexes[file2Column];
            let file2Value = 'COLUMN NOT FOUND';
            
            if (file2Index !== undefined) {
                file2Value = row2[file2Index] || '';
            }
            
            columnDetails.push({
                mappedColumnName: `${file1Column} ↔ ${file2Column}`,
                file1Column,
                file2Column,
                file1Value: 'ROW NOT FOUND',
                file2Value,
                match: false,
                status: 'Missing in Data File 1'
            });
        });
        
        comparedRows.push({
            rowNumber: comparedRows.length + 1,
            idValue: idValue2,
            keyValue: idValue2,
            matches: false,
            status: 'Missing in Data File 1',
            columnDetails: columnDetails,
            mismatches: []
        });
    }
    
    // Calculate results
    results.allData = comparedRows;
    results.totalRows = comparedRows.length;
    results.matchedRows = comparedRows.filter(row => row.matches).length;
    results.mismatchedRows = comparedRows.filter(row => !row.matches).length;
    
    // Extract all mismatches
    results.mismatches = [];
    comparedRows.forEach(row => {
        if (row.mismatches && row.mismatches.length > 0) {
            results.mismatches.push(...row.mismatches);
        }
    });
    
    console.log(`Comparison complete: ${results.totalRows} compared rows, ${results.matchedRows} matches, ${results.mismatchedRows} mismatches`);
    return results;
}

function displayResults(results) {
    console.log('Displaying results:', results);
    
    // Show results sections
    document.getElementById('summarySection').classList.remove('hidden');
    document.getElementById('resultsSection').classList.remove('hidden');
    
    // Update only the statistics that exist (just totalRows now)
    const totalRowsElement = document.getElementById('totalRows');
    if (totalRowsElement) {
        totalRowsElement.textContent = results.totalRows;
    }
    
    // Check if all rows match
    const allMatch = results.matchedRows > 0 && results.mismatchedRows === 0;
    const successMessage = document.getElementById('successMessage');
    
    if (allMatch) {
        if (successMessage) {
            successMessage.classList.remove('hidden');
        }
        const resultsTable = document.getElementById('resultsTable');
        if (resultsTable) {
            resultsTable.innerHTML = '';
        }
    } else {
        if (successMessage) {
            successMessage.classList.add('hidden');
        }
        // Display the side-by-side comparison
        displaySideBySideComparison(results);
    }
    
    console.log('Results displayed successfully');
}

// Removed old placeholder function - using the new implementation above

function downloadMismatchedRecords() {
    if (!comparisonResults || !comparisonResults.mismatches.length) {
        showError('No mismatched records to download');
        return;
    }
    
    try {
        const wb = XLSX.utils.book_new();
        
            const mismatchedData = comparisonResults.mismatches.map(mismatch => ({
                'Row Number': mismatch.rowNumber,
                'Column': mismatch.column,
            'File 1 Value': mismatch.file1Value,
            'File 2 Value': mismatch.file2Value,
                'Status': mismatch.status
            }));
            
        const ws = XLSX.utils.json_to_sheet(mismatchedData);
        XLSX.utils.book_append_sheet(wb, ws, 'Mismatched Records');
        
        const fileName = `comparison_results_${new Date().toISOString().split('T')[0]}.xlsx`;
        XLSX.writeFile(wb, fileName);
        
    } catch (error) {
        console.error('Download error:', error);
        showError('Error downloading file');
    }
}

function downloadComparedFileAllRows() {
    console.log('Downloading all compared rows...');
    alert('Download all compared rows logic not yet implemented.');
}

function downloadComparisonResults() {
    console.log('📥 Starting download of comparison table...');
    
    // Find the current comparison tables (headers and data)
    const dataTable = document.querySelector('#file1Data table') ||
                      document.querySelector('#resultsTable table') ||
                      document.querySelector('#sideBySideComparison .comparison-table') ||
                      document.querySelector('.comparison-table');
    const headerTable = document.querySelector('#file1Headers table');
    if (!dataTable) {
        showError('No comparison table found to download');
        return;
    }
    
    try {
        const wb = XLSX.utils.book_new();
        
        // Get current statistics
        const totalRowsElement = document.getElementById('totalRows');
        const matchedRowsElement = document.getElementById('matchedRows');
        const mismatchedRowsElement = document.getElementById('mismatchedRows');
        const accuracyElement = document.getElementById('accuracyPercentage');
        
        const totalRows = totalRowsElement ? parseInt(totalRowsElement.textContent) || 0 : 0;
        const matchedRows = matchedRowsElement ? parseInt(matchedRowsElement.textContent) || 0 : 0;
        const mismatchedRows = mismatchedRowsElement ? parseInt(mismatchedRowsElement.textContent) || 0 : 0;
        const accuracy = accuracyElement ? accuracyElement.textContent || '0%' : '0%';
        
        // Create summary sheet
        const summaryData = [
            ['Comparison Summary', ''],
            ['Total Rows Compared', totalRows],
            ['Matched Rows', matchedRows],
            ['Mismatched Rows', mismatchedRows],
            ['Accuracy', accuracy],
            ['Generated Date', new Date().toLocaleString()],
            ['', ''],
            ['Note:', ''],
            ['This export contains the exact table as displayed in the application', '']
        ];
        
        const summaryWs = XLSX.utils.aoa_to_sheet(summaryData);
        XLSX.utils.book_append_sheet(wb, summaryWs, 'Summary');
        
        // Extract table data exactly as displayed
        const tableData = [];
        
        // Build header from visible header table or first row of data table
        let headerCells = null;
        if (headerTable) {
            const headerRow = headerTable.querySelector('thead tr');
            if (headerRow) headerCells = headerRow.querySelectorAll('th');
        }
        if (!headerCells) {
            const hdr = dataTable.querySelector('thead tr');
            if (hdr) headerCells = hdr.querySelectorAll('th');
        }
        if (headerCells && headerCells.length) {
            const headers = [];
            headerCells.forEach(cell => headers.push((cell.textContent || '').trim()));
            tableData.push(headers);
        }
        
        // Get data rows
        const dataRows = dataTable.querySelectorAll('tbody tr');
        dataRows.forEach(row => {
            const rowData = [];
            const cells = row.querySelectorAll('td');
            cells.forEach(cell => rowData.push((cell.textContent || '').trim()));
            if (rowData.length > 0) tableData.push(rowData);
        });
        
        // Create worksheet from table data
        const tableWs = XLSX.utils.aoa_to_sheet(tableData);
        
        // Style the header row
        if (tableData.length > 0) {
            const range = XLSX.utils.decode_range(tableWs['!ref']);
            for (let col = range.s.c; col <= range.e.c; col++) {
                const cellAddress = XLSX.utils.encode_cell({ r: 0, c: col });
                if (!tableWs[cellAddress]) tableWs[cellAddress] = {};
                tableWs[cellAddress].s = {
                    font: { bold: true },
                    fill: { fgColor: { rgb: "E3F2FD" } },
                    alignment: { horizontal: "center" }
                };
            }
            
            // Auto-fit columns
            const cols = [];
            for (let col = range.s.c; col <= range.e.c; col++) {
                let maxLength = 10; // minimum column width
                for (let row = range.s.r; row <= range.e.r; row++) {
                    const cellAddress = XLSX.utils.encode_cell({ r: row, c: col });
                    if (tableWs[cellAddress] && tableWs[cellAddress].v) {
                        const cellLength = String(tableWs[cellAddress].v).length;
                        maxLength = Math.max(maxLength, cellLength);
                    }
                }
                cols.push({ wch: Math.min(maxLength + 2, 50) }); // max 50 chars width
            }
            tableWs['!cols'] = cols;
        }
        
        XLSX.utils.book_append_sheet(wb, tableWs, 'Joint File Comparison');
        
        // Generate filename with timestamp
        const timestamp = new Date().toISOString().split('T')[0];
        const fileName = `Joint_File_Comparison_${timestamp}.xlsx`;
        
        // Download the file
        XLSX.writeFile(wb, fileName);
        
        showSuccessNotification(`Table downloaded successfully as ${fileName}`);
        
    } catch (error) {
        console.error('❌ Download error:', error);
        showError(`Error downloading table: ${error.message}`);
    }
}

function downloadIntermediateAllRows() {
    console.log('Downloading all intermediate rows...');
    alert('Download all intermediate rows logic not yet implemented.');
}

function downloadIntermediateFiles() {
    console.log('Downloading intermediate files...');
    alert('Download intermediate files logic not yet implemented.');
}

function updateIntermediateButtonState() {
    // Logic to update the button state based on file uploads
    console.log('Update intermediate button state logic not yet implemented.');
}

function updateIntermediateTabWithMapping() {
    // Logic to update the UI with mapping information
    console.log('Update intermediate tab with mapping logic not yet implemented.');
}

// Removed duplicate showSuccessNotification function - using the main one above

// Removed duplicate showError function - using the main one above

</script>
</body>
</html>
"""

components.html(HTML_DOC, height=900, scrolling=True)
