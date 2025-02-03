<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>创意AI工具导航</title>
    <style>
        :root {
            --primary-color: #2d8cf0;
            --border-color: #e0e0e0;
            --sidebar-width: 280px;
        }

        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: "Microsoft YaHei", sans-serif;
        }

        body {
            min-height: 100vh;
            background: #f8f9fa;
            position: relative;
        }

        /* 改进的侧边栏系统 */
        .tree-container {
            width: var(--sidebar-width);
            background: #fff;
            border-right: 1px solid var(--border-color);
            position: fixed;
            left: 0;
            top: 0;
            bottom: 0;
            transition: left 0.3s ease;
            z-index: 1000;
            overflow-y: auto;
        }

        .collapsed {
            left: calc(-1 * var(--sidebar-width));
        }

        /* 始终可见的折叠按钮 */
        #toggleSidebar {
            position: fixed;
            left: 20px;
            top: 20px;
            background: white;
            border: 1px solid var(--border-color);
            border-radius: 4px;
            font-size: 20px;
            width: 36px;
            height: 36px;
            cursor: pointer;
            z-index: 1001;
            box-shadow: 0 2px 8px rgba(0,0,0,0.1);
            transition: all 0.3s ease;
        }

        #toggleSidebar:hover {
            background: var(--primary-color);
            color: white;
        }

        /* 树形菜单样式 */
        .tree-menu {
            list-style: none;
            padding: 60px 16px 20px; /* 给顶部留出空间 */
        }

        .tree-node {
            position: relative;
            padding-left: 24px;
            margin: 8px 0;
        }

        .tree-toggle {
            position: absolute;
            left: -16px;
            top: 4px;
            width: 20px;
            height: 20px;
            border: 1px solid var(--border-color);
            border-radius: 4px;
            text-align: center;
            line-height: 18px;
            cursor: pointer;
            background: #fff;
        }

        .tree-content {
            padding: 8px;
            border-radius: 4px;
            display: block;
            color: #333;
            text-decoration: none;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .tree-content:hover {
            background: #f0f7ff;
        }

        .tree-content.product {
            color: var(--primary-color);
            padding-left: 32px;
        }

        .tree-children {
            list-style: none;
            padding-left: 24px;
            display: none;
        }

        .tree-children.open {
            display: block;
        }

        /* 内容区域 */
        .content {
            margin-left: var(--sidebar-width);
            transition: margin-left 0.3s ease;
            min-height: 100vh;
        }

        .collapsed + .content {
            margin-left: 0;
        }

        #contentFrame {
            width: 100%;
            height: 100vh;
            border: none;
        }

        /* 浏览器兼容性处理 */
        @media screen and (-webkit-min-device-pixel-ratio:0) {
            /* Safari/Chrome specific styles */
            .tree-container {
                -webkit-transition: left 0.3s ease;
            }
        }

        @-moz-document url-prefix() {
            /* Firefox specific styles */
            .tree-container {
                -moz-transition: left 0.3s ease;
            }
        }

        @media all and (-ms-high-contrast: none), (-ms-high-contrast: active) {
            /* IE10+ CSS styles */
            .tree-container {
                transition: none;
            }
            .content {
                margin-left: 280px;
            }
            .collapsed + .content {
                margin-left: 0;
            }
        }
    </style>
</head>
<body>
    <button id="toggleSidebar">☰</button>
    
    <nav class="tree-container">
        <div class="sidebar-header">
            <span>大壮创意AI工具</span>
        </div>
        <ul class="tree-menu" id="treeMenu"></ul>
    </nav>

    <div class="content">
        <iframe name="contentFrame" id="contentFrame" src="about:blank"></iframe>
    </div>

<script>
// 精选产品数据
const treeData = [
    {
        name: "文案写作",
        icon: "📝",
        children: [
            {
                name: "文心一言",
                url: "https://yiyan.baidu.com",
                icon: "✍️"
            },
            {
                name: "火山写作",
                url: "https://www.writingo.net",
                icon: "📖"
            }
        ]
    },
    {
        name: "设计创意",
        icon: "🎨",
        children: [
            {
                name: "稿定AI设计",
                url: "https://www.gaoding.com/ai",
                icon: "🖌️"
            },
            {
                name: "美图AI",
                url: "https://ai.meitu.com",
                icon: "🖼️"
            }
        ]
    }
];

// 改进的树形菜单渲染
function renderTree(data, level = 0) {
    return data.map(node => {
        const hasChildren = node.children?.length > 0;
        return `
            <li class="tree-node">
                ${hasChildren ? `
                    <span class="tree-toggle">+</span>
                ` : ''}
                ${node.url ? `
                    <a href="${node.url}" target="contentFrame" 
                       class="tree-content ${hasChildren ? '' : 'product'}"
                       rel="noopener noreferrer">
                        ${node.icon || '🔗'} ${node.name}
                    </a>
                ` : `
                    <span class="tree-content">
                        ${node.icon || '📁'} ${node.name}
                    </span>
                `}
                ${hasChildren ? `
                    <ul class="tree-children">
                        ${renderTree(node.children, level + 1)}
                    </ul>
                ` : ''}
            </li>
        `;
    }).join('');
}

// 增强的初始化函数
function initApp() {
    const treeMenu = document.getElementById('treeMenu');
    treeMenu.innerHTML = renderTree(treeData);

    // 树形菜单交互
    treeMenu.addEventListener('click', function(e) {
        const toggle = e.target.closest('.tree-toggle');
        if (toggle) {
            const ul = toggle.parentElement.querySelector('.tree-children');
            if (ul) {
                ul.classList.toggle('open');
                toggle.textContent = ul.classList.contains('open') ? '-' : '+';
            }
        }
    });

    // 侧边栏状态管理
    const toggleBtn = document.getElementById('toggleSidebar');
    const sidebar = document.querySelector('.tree-container');
    let isCollapsed = localStorage.getItem('sidebarCollapsed') === 'true';

    function updateSidebar() {
        sidebar.classList.toggle('collapsed', isCollapsed);
        toggleBtn.textContent = isCollapsed ? '»' : '☰';
        localStorage.setItem('sidebarCollapsed', isCollapsed);
    }

    toggleBtn.addEventListener('click', () => {
        isCollapsed = !isCollapsed;
        updateSidebar();
    });

    // 初始化状态
    updateSidebar();

    // 默认展开第一级菜单
    document.querySelectorAll('.tree-toggle').forEach(toggle => {
        const ul = toggle.parentElement.querySelector('.tree-children');
        if (ul) {
            ul.classList.add('open');
            toggle.textContent = '-';
        }
    });

    // IE兼容处理
    if (navigator.userAgent.match(/Trident\/7\./)) {
        document.body.style.minHeight = '100vh';
    }
}

document.addEventListener('DOMContentLoaded', initApp);
</script>
</body>
</html>
