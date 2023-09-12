<h1>Bricks-Anchor-Navigation</h1>
<h2>The perfect Bricks Builder Addon for Local Navigation Menus (Anchor Links)</h2><br>
For Bricks Builder enthusiasts who've been grappling with local ID (anchor) navigation woes, the Bricks Anchor-Menu Navigator is here to revitalize your web building experience.
<br><br>
<b>What it Fixes:</b>

<h3>This is a normal Wordpress Menu with Local (Anchor) Links</h3>
<img src="https://github.com/werkmind/Bricks-Anchor-Navigation/blob/main/wpmenu.png" height="300px"/>
The Problem: If the Anchor IDs are on the same Page, all appear Active in Bricks, even if they are not in the viewport.
<h6>Without the Plugin:</h6>
<img src="https://github.com/werkmind/Bricks-Anchor-Navigation/blob/main/without.jpg" height="300px"/>
<br><hl></hl>
<br>
<h6>With the Plugin:</h6>
<img src="https://github.com/werkmind/Bricks-Anchor-Navigation/blob/main/withplugin.gif" width="100%"/>
<p><i>The Plugin updates the URL on Scroll. Multipage Navigation is fixed. Anchor Links Menu is fixed.</i></p><br>

<ul>
  <li><b>Erratic URL Behavior:</b><br> Without this plugin, navigating between sections doesn't update the URL, leaving users and site managers in a navigation limbo.</li>
<br><br>
<li><b>Unspecific Anchor Listening:</b><br> Previously, any anchor was up for grabs. Now, get precise with which anchors you want to be active, ensuring that you're in control at all times.</li>
<br><br>
<li><b>Unintended Active Menu Items:</b><br> Ever found all your menu items oddly highlighted at once? This hiccup distorts the active typography of anchor navigation links. Our plugin ensures that only the relevant menu item stands out, retaining your site's aesthetic integrity.</li>
  <br><br>
</ul>
Don't let these common glitches disrupt your site's navigation harmony. With the Bricks Anchor-Menu Navigator, sail smoothly from one section to another, and ensure your URL and menu items reflect your actual navigation. Build better, smarter, and glitch-free with Bricks!


<h3>Prerequisites:</h3>
<ol>
    <li>Ensure you have <a href="https://wordpress.org/download/" target="_blank">WordPress</a> installed.</li>
    <li>Ensure you are using the <a href="https://www.bricksbuilder.io/" target="_blank">Bricks Builder</a> for your WordPress site.</li>
</ol>

<h3>Installation Steps:</h3>

<h4>1. Download the Plugin</h4>
<ul>
    <li>Click on the 'Release' button on the <a href="https://github.com/werkmind/Bricks-Anchor-Navigation/" target="_blank">Bricks Anchor-Menu Navigator GitHub repository</a>.</li>
    <li>Download the latest release zip.</li>
    <li>Save the ZIP file to your computer.</li>
</ul>

<h4>2. Install the Plugin on WordPress</h4>
<ul>
    <li>Log in to your WordPress dashboard.</li>
    <li>Navigate to <code>Plugins > Add New</code>.</li>
    <li>Click the <code>Upload Plugin</code> button at the top of the page.</li>
    <li>Click the <code>Choose File</code> button and select the downloaded ZIP file.</li>
    <li>Once uploaded, click <code>Install Now</code>.</li>
</ul>

<h4>3. Activate the Plugin</h4>
<ul>
    <li>After the installation completes, click <code>Activate Plugin</code>.</li>
</ul>

<h4>4. Configure the Plugin (Optional)</h4>
<p>In your WordPress dashboard, navigate to <code>Settings > Bricks Navigator</code>. From here, you can:</p>
<ul>
    <li>Specify which anchor links the plugin should listen to.
    <br> for example: #link1 (anchor link of the ID)</li>
    <li>Update the menu item selector if it's different from the default <code>.bricks-nav-menu li</code>.</li>
</ul>
<br><br><br><br><br>
<h3>If you want, you can also use this as a Code-Snippet.</h3>
<p>Just paste this Code into <code>Bricks Builder > Settings > Custom Code > Body Footer Scripts</code></p>

```js

<script>
document.addEventListener("DOMContentLoaded", function() {
  //Change this to choose another CSS Selector
    const menuItems = document.querySelectorAll(".bricks-nav-menu li");
  //Change this to select the Anchors that this script listens to
    const validHashes = ['#leistungen', '#link1', '#link2', '#link3'];

//Do not change any other code from now on if everything works
    let isScrolling = false;
    let targetHashWhileScrolling = null;

    function setActiveClass(anchor) {
        menuItems.forEach(menuItem => {
            const link = menuItem.querySelector("a");
            const expectedHref = window.location.origin + window.location.pathname + anchor;

            if (link && link.getAttribute("href") === expectedHref) {
                menuItem.classList.add("current-menu-item");
            } else {
                menuItem.classList.remove("current-menu-item");
            }
        });
    }

    function getSectionInView() {
        for(let hash of validHashes) {
            const section = document.querySelector(hash);
            if (!section) continue;

            const rect = section.getBoundingClientRect();
              //Change the Factor 0.2 for more space from top to listen to anchors - this is 20%
            if (rect.top >= 0 && rect.top <= window.innerHeight * 0.2) {
                return hash;
            }
        }
        return null;
    }

    window.addEventListener('scroll', function() {
        const currentSection = getSectionInView();
        
        if (isScrolling) {
            if (currentSection && currentSection === targetHashWhileScrolling) {
                isScrolling = false;
                sessionStorage.removeItem('programmaticScroll');
                targetHashWhileScrolling = null;
                history.pushState({}, '', currentSection);
                setActiveClass(currentSection);
            }
        } else if (currentSection && !isScrolling) {
            history.pushState({}, '', currentSection);
            setActiveClass(currentSection);
        }
    });

    menuItems.forEach(menuItem => {
        const link = menuItem.querySelector("a");
        link.addEventListener("click", function(e) {
            const href = link.getAttribute("href");
            const targetHash = href.includes('#') ? '#' + href.split('#')[1] : '';

            if (validHashes.includes(targetHash)) {
                e.preventDefault();

                if (window.location.pathname === '/') {
                    const targetSection = document.querySelector(targetHash);
                    if (targetSection) {
                        isScrolling = true;
                        targetHashWhileScrolling = targetHash;
                        window.scrollTo({
                            top: targetSection.offsetTop,
                            behavior: 'smooth'
                        });
                    }
                } else {
                    sessionStorage.setItem('programmaticScroll', targetHash);
                    window.location.href = window.location.origin + "/" + targetHash;
                }
            } else {
                window.location.href = href;
            }
        });
    });

    const initialHash = window.location.hash;
    if (validHashes.includes(initialHash)) {
        if (sessionStorage.getItem('programmaticScroll') === initialHash) {
            isScrolling = true;
            targetHashWhileScrolling = initialHash;
        }
        setActiveClass(initialHash);
    } else {
        setActiveClass("");
    }
});
</script>

```



