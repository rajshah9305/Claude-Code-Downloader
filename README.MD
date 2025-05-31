# 🚀 Claude Code Downloader

A browser-based tool that automatically extracts and downloads all code blocks and artifacts from your Claude AI conversations. Perfect for developers who want to quickly save code snippets, projects, and examples shared in Claude chats.

## ✨ Features

- **Automatic Code Detection**: Finds all code blocks, artifacts, and code snippets in Claude conversations
- **Smart Language Detection**: Automatically detects programming languages and assigns appropriate file extensions
- **Bulk Download**: Downloads all code as separate files in one click
- **Multiple Format Support**: Supports 25+ programming languages including Python, JavaScript, HTML, CSS, Java, C++, and more
- **Artifact Support**: Extracts code from Claude's interactive artifacts
- **Duplicate Prevention**: Handles duplicate filenames with automatic timestamps
- **Zero Installation**: Works directly in your browser - no downloads or installations required

## 🎯 Supported Languages

The tool automatically detects and properly names files for these languages:

| Language | Extension | Language | Extension | Language | Extension |
|----------|-----------|----------|-----------|----------|-----------|
| JavaScript | `.js` | Python | `.py` | HTML | `.html` |
| CSS | `.css` | Java | `.java` | C++ | `.cpp` |
| C | `.c` | PHP | `.php` | Ruby | `.rb` |
| Go | `.go` | Rust | `.rs` | TypeScript | `.ts` |
| JSX | `.jsx` | TSX | `.tsx` | SQL | `.sql` |
| JSON | `.json` | XML | `.xml` | YAML | `.yml` |
| Markdown | `.md` | Bash | `.sh` | PowerShell | `.ps1` |
| R | `.r` | MATLAB | `.m` | Swift | `.swift` |
| Kotlin | `.kt` | Scala | `.scala` | Dart | `.dart` |

## 🚀 Quick Start

### Method 1: Use the HTML Interface (Recommended)

1. **Download the HTML file**: Save `claude_code_downloader.html` to your computer
2. **Open it**: Double-click the file to open it in your browser
3. **Follow the instructions**: The page provides step-by-step guidance
4. **Copy the script**: Click the "Copy Script to Clipboard" button
5. **Go to Claude**: Navigate to claude.ai and open your conversation
6. **Open Console**: Press F12 (or Ctrl+Shift+I on Windows, Cmd+Option+I on Mac)
7. **Paste & Run**: Paste the script in the Console tab and press Enter

### Method 2: Direct Script Usage

1. Go to your Claude conversation at claude.ai
2. Open browser developer console (F12)
3. Copy and paste this script:

```javascript
(function() {
    console.log('🚀 Starting Claude Code Downloader...');
    
    let downloadCount = 0;
    const downloadedFiles = new Set();
    
    function getFileExtension(language) {
        const extensions = {
            'javascript': 'js', 'python': 'py', 'html': 'html',
            'css': 'css', 'java': 'java', 'cpp': 'cpp',
            'c': 'c', 'php': 'php', 'ruby': 'rb',
            'go': 'go', 'rust': 'rs', 'typescript': 'ts',
            'jsx': 'jsx', 'tsx': 'tsx', 'sql': 'sql',
            'json': 'json', 'xml': 'xml', 'yaml': 'yml',
            'markdown': 'md', 'bash': 'sh', 'shell': 'sh',
            'powershell': 'ps1', 'r': 'r', 'matlab': 'm',
            'swift': 'swift', 'kotlin': 'kt', 'scala': 'scala',
            'dart': 'dart'
        };
        return extensions[language?.toLowerCase()] || 'txt';
    }
    
    function sanitizeFilename(filename) {
        return filename.replace(/[^a-z0-9.-]/gi, '_').replace(/_+/g, '_');
    }
    
    function downloadFile(content, filename) {
        if (downloadedFiles.has(filename)) {
            const timestamp = new Date().getTime();
            filename = filename.replace(/(\.[^.]+)$/, `_${timestamp}$1`);
        }
        downloadedFiles.add(filename);
        
        const blob = new Blob([content], { type: 'text/plain' });
        const url = window.URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = filename;
        document.body.appendChild(a);
        a.click();
        document.body.removeChild(a);
        window.URL.revokeObjectURL(url);
        downloadCount++;
        console.log(`✅ Downloaded: ${filename}`);
    }
    
    function extractCodeBlocks() {
        const codeBlocks = document.querySelectorAll('pre code, pre[class*="language-"], code[class*="language-"]');
        console.log(`Found ${codeBlocks.length} code blocks`);
        
        codeBlocks.forEach((block, index) => {
            const content = block.textContent || block.innerText;
            if (content.trim().length < 10) return;
            
            let language = 'txt';
            const classNames = (block.className || block.parentElement?.className || '').toLowerCase();
            
            const langPatterns = [
                /language-(\w+)/, /lang-(\w+)/,
                /highlight-(\w+)/, /(\w+)-code/
            ];
            
            for (const pattern of langPatterns) {
                const match = classNames.match(pattern);
                if (match) {
                    language = match[1];
                    break;
                }
            }
            
            if (language === 'txt') {
                const firstLine = content.split('\n')[0].toLowerCase();
                if (firstLine.includes('def ') || firstLine.includes('import ') || content.includes('print(')) {
                    language = 'python';
                } else if (firstLine.includes('function') || content.includes('console.log') || content.includes('const ') || content.includes('let ')) {
                    language = 'javascript';
                } else if (content.includes('<!DOCTYPE') || content.includes('<html')) {
                    language = 'html';
                } else if (content.includes('SELECT') || content.includes('INSERT') || content.includes('CREATE TABLE')) {
                    language = 'sql';
                } else if (firstLine.includes('#include') || content.includes('int main(')) {
                    language = 'cpp';
                }
            }
            
            const extension = getFileExtension(language);
            const filename = sanitizeFilename(`claude_code_${index + 1}.${extension}`);
            downloadFile(content, filename);
        });
    }
    
    function extractArtifacts() {
        const artifacts = document.querySelectorAll('[data-testid*="artifact"], .artifact, [class*="artifact"]');
        console.log(`Found ${artifacts.length} potential artifacts`);
        
        artifacts.forEach((artifact, index) => {
            const codeElement = artifact.querySelector('pre, code, textarea');
            if (codeElement) {
                const content = codeElement.textContent || codeElement.innerText || codeElement.value;
                if (content && content.trim().length >= 10) {
                    let extension = 'txt';
                    const artifactType = artifact.getAttribute('data-type') || artifact.className;
                    
                    if (artifactType.includes('react') || content.includes('import React')) {
                        extension = 'jsx';
                    } else if (artifactType.includes('html') || content.includes('<!DOCTYPE') || content.includes('<html')) {
                        extension = 'html';
                    } else if (content.includes('def ') || content.includes('import ')) {
                        extension = 'py';
                    } else if (content.includes('function') || content.includes('const ') || content.includes('let ')) {
                        extension = 'js';
                    }
                    
                    const filename = sanitizeFilename(`claude_artifact_${index + 1}.${extension}`);
                    downloadFile(content, filename);
                }
            }
        });
    }
    
    setTimeout(() => {
        try {
            extractCodeBlocks();
            extractArtifacts();
            
            if (downloadCount === 0) {
                console.log('❌ No code found in this conversation');
                alert('No code found in this Claude conversation. Make sure you\'re on a conversation page with code blocks or artifacts.');
            } else {
                console.log(`🎉 Successfully downloaded ${downloadCount} files!`);
                alert(`Success! Downloaded ${downloadCount} code files. Check your Downloads folder.`);
            }
        } catch (error) {
            console.error('❌ Error:', error);
            alert('An error occurred. Please check the browser console for details.');
        }
    }, 1000);
})();
```

4. Press Enter to run the script
5. Files will automatically download to your Downloads folder

## 📋 How It Works

1. **Scans the Page**: The script searches for code blocks, artifacts, and code snippets in your Claude conversation
2. **Detects Languages**: Uses CSS classes, content analysis, and patterns to identify programming languages
3. **Creates Files**: Generates appropriately named files with correct extensions
4. **Downloads Automatically**: Uses browser's download functionality to save all files at once

## 🛠️ Browser Compatibility

| Browser | Status | Console Shortcut |
|---------|---------|------------------|
| Chrome | ✅ Fully Supported | F12 or Ctrl+Shift+I |
| Firefox | ✅ Fully Supported | F12 or Ctrl+Shift+K |
| Safari | ✅ Supported* | Cmd+Option+I |
| Edge | ✅ Fully Supported | F12 or Ctrl+Shift+I |

*Safari requires enabling the Developer menu first: Safari → Preferences → Advanced → Show Develop menu

## 🔧 Troubleshooting

### No Files Downloaded?
- Ensure you're on a Claude conversation page (claude.ai)
- Make sure the conversation contains code blocks or artifacts
- Check if your browser blocked multiple downloads (allow when prompted)

### Console Errors?
- Refresh the Claude page and try again
- Make sure you're copying the complete script
- Check that JavaScript is enabled in your browser

### Wrong File Extensions?
- The script uses smart detection but may default to `.txt` for unknown languages
- You can manually rename files after download if needed

### Downloads Not Starting?
- Check your browser's download settings
- Ensure pop-ups and downloads are allowed for claude.ai
- Try running the script again after a page refresh

## 📁 File Naming Convention

Downloaded files follow this naming pattern:
- **Code blocks**: `claude_code_1.py`, `claude_code_2.js`, etc.
- **Artifacts**: `claude_artifact_1.jsx`, `claude_artifact_2.html`, etc.
- **Duplicates**: `claude_code_1_1640995200000.py` (timestamp added)

## ⚠️ Important Notes

- **Only works on claude.ai**: This script is specifically designed for the Claude web interface
- **Client-side only**: No data is sent to external servers - everything runs in your browser
- **Download limits**: Some browsers may limit concurrent downloads - allow when prompted
- **Large conversations**: May take a few seconds to process conversations with many code blocks

## 🤝 Contributing

Found a bug or want to improve the tool? Contributions are welcome!

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## 📝 License

This project is open source and available under the MIT License.

## 🆘 Support

If you encounter issues:

1. Check the troubleshooting section above
2. Look at browser console for error messages
3. Try refreshing the page and running the script again
4. Open an issue on GitHub with details about your problem

## 🎉 Credits

Created for developers who work with Claude AI and need an easy way to export their code conversations. Built with vanilla JavaScript for maximum compatibility and zero dependencies.

---

**Happy Coding!** 🚀
