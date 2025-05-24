### Integrating WIRIS Formula Editor with TinyMCE in a React Application: A Step-by-Step Guide

#### Overview
The goal is to enhance a React-based form with a rich text editor (TinyMCE) by integrating the WIRIS Formula Editor plugin, enabling users to create and edit mathematical and chemical equations within the editor. TinyMCE is a popular WYSIWYG editor that can be extended with plugins like WIRIS to support advanced features such as equation editing, which is essential for academic or scientific applications (e.g., research paper submissions). This report outlines the process of integrating WIRIS into a TinyMCE editor within a React application, addressing potential issues like script loading errors, and provides the commands needed to set up the environment.

---

#### Step 1: Understand the Components and Requirements
To integrate the WIRIS Formula Editor, you need the following components:
- **React**: A JavaScript library for building user interfaces. Your application uses React to render a dynamic form (`PublicationForm.js`).
- **TinyMCE**: A rich text editor that provides a WYSIWYG interface for text editing. In React, TinyMCE is integrated using the `@tinymce/tinymce-react` package, which provides a component (`Editor`) to embed TinyMCE in a React app.
- **WIRIS Plugin**: A third-party plugin (`@wiris/mathtype-tinymce6`) that extends TinyMCE to support math and chemistry formula editing. WIRIS adds buttons to the TinyMCE toolbar for launching its formula editor, which allows users to create equations that are inserted as images or MathML into the editor content.
- **Development Environment**: A typical React setup, such as one created with Create React App, running on `http://localhost:3001`, with a backend server (e.g., Django) at `http://127.0.0.1:8000/` for form submission.

The integration involves:
1. Installing and setting up TinyMCE in your React project.
2. Adding the WIRIS plugin to TinyMCE.
3. Configuring the TinyMCE editor to include WIRIS buttons in the toolbar.
4. Handling script loading to avoid errors like `Unexpected token '<'`.

---

#### Step 2: Set Up TinyMCE in Your React Project
TinyMCE needs to be installed and integrated into your React application before adding the WIRIS plugin.

1. **Install TinyMCE Packages**:
   - Use the `@tinymce/tinymce-react` package, which provides the `Editor` component for React.
   - Optionally, install the `tinymce` package to serve TinyMCE scripts locally (though the TinyMCE CDN can also be used).

   **Command**:
   ```bash
   npm install @tinymce/tinymce-react tinymce
   ```

2. **Serve TinyMCE Locally (Optional)**:
   - If you prefer to serve TinyMCE scripts locally (instead of using the CDN), copy the TinyMCE files to your `public` folder. This ensures that the scripts are available at a URL like `http://localhost:3001/tinymce/tinymce.min.js`.
   - In a Create React App setup, the `public` folder is served statically, so any files placed there are accessible via the root URL.

   **Command**:
   ```bash
   cp -r node_modules/tinymce public/tinymce
   ```

   After running this, confirm that `public/tinymce/tinymce.min.js` exists. This file will be loaded by TinyMCE when the editor initializes.

3. **Integrate TinyMCE into Your Component**:
   - In your React component (e.g., `PublicationForm.js`), import the `Editor` component from `@tinymce/tinymce-react`.
   - Use the `Editor` component in your JSX where you want the rich text editor to appear. For example, in your form, you render the editor for fields of type `"editor"`.
   - Configure the editor with an `init` object that specifies its settings, such as height, plugins, toolbar, and custom buttons.

   **Key Configuration**:
   - Set `apiKey` to your TinyMCE API key (or use the default one for testing, though you should get your own from TinyMCE’s website for production).
   - Disable the menubar and status bar for a cleaner UI (`menubar: false`, `statusbar: false`).
   - Specify plugins like `table` for table editing and add a `toolbar` string with basic buttons (e.g., `undo redo | bold italic`).

   At this stage, your TinyMCE editor should render with basic functionality, such as text formatting and table insertion, but without formula editing capabilities.

---

#### Step 3: Install and Set Up the WIRIS Plugin
The WIRIS plugin (`@wiris/mathtype-tinymce6`) is designed for TinyMCE 6 and provides the formula editing features you need.

1. **Install the WIRIS Plugin**:
   - Install the `@wiris/mathtype-tinymce6` package, which contains the WIRIS plugin compatible with TinyMCE 6.

   **Command**:
   ```bash
   npm install @wiris/mathtype-tinymce6
   ```

2. **Copy WIRIS Plugin to the Public Folder**:
   - TinyMCE loads external plugins via a URL, so you need to make the WIRIS plugin script accessible. Copy the `plugin.min.js` file from the WIRIS package to your `public` folder.
   - Create a `wiris` directory inside `public` to keep things organized.

   **Command**:
   ```bash
   mkdir -p public/wiris
   cp node_modules/@wiris/mathtype-tinymce6/plugin.min.js public/wiris/plugin.min.js
   ```

   After this, confirm that `public/wiris/plugin.min.js` exists. This file will be loaded by TinyMCE at the URL `http://localhost:3001/wiris/plugin.min.js`.

---

#### Step 4: Configure TinyMCE to Use the WIRIS Plugin
Now, modify the TinyMCE configuration in your React component to include the WIRIS plugin and add its formula editor buttons to the toolbar.

1. **Add WIRIS to the Plugins List**:
   - In the `init` object of the `Editor` component, update the `plugins` array to include `"tiny_mce_wiris"`. This tells TinyMCE to load the WIRIS plugin.
   - Example: `plugins: ["table", "tiny_mce_wiris"]`.
   - The `table` plugin remains for table editing, and `tiny_mce_wiris` adds the formula editing functionality.

2. **Load the WIRIS Plugin as an External Plugin**:
   - Since the WIRIS plugin is not bundled with TinyMCE, you need to specify its location using the `external_plugins` option.
   - Set `external_plugins` to an object where the key is the plugin name (`tiny_mce_wiris`) and the value is the URL to the plugin script.
   - Example: `external_plugins: { tiny_mce_wiris: "/wiris/plugin.min.js" }`.
   - The URL `/wiris/plugin.min.js` resolves to `http://localhost:3001/wiris/plugin.min.js` in your development environment, pointing to the file you copied to the `public` folder.

3. **Add WIRIS Buttons to the Toolbar**:
   - The WIRIS plugin provides two main buttons for formula editing:
     - `tiny_mce_wiris_formulaEditor`: For general math equations (e.g., fractions, integrals).
     - `tiny_mce_wiris_formulaEditorChemistry`: For chemical formulas (e.g., molecular structures, chemical equations).
   - Update the `toolbar` string in the `init` object to include these buttons.
   - Example: `toolbar: "undo redo | bold italic | tiny_mce_wiris_formulaEditor tiny_mce_wiris_formulaEditorChemistry | table tableprops tabledelete"`.
   - This toolbar configuration includes:
     - Undo and redo buttons for basic editing.
     - Bold and italic buttons for text formatting.
     - The WIRIS formula editor buttons for math and chemistry.
     - Table-related buttons for inserting and managing tables.
   - The `|` character in the toolbar string creates visual separators between groups of buttons for better organization.

4. **Configure the WIRIS Service Endpoint**:
   - The WIRIS plugin requires a service endpoint to render equations. This is configured using the `mathTypeParameters` option in the `init` object.
   - Set `mathTypeParameters` to specify the WIRIS service URL and server type.
   - Example:
     - `URI`: `"https://www.wiris.net/demo/plugins/app/configurationjs"` (the default WIRIS demo endpoint for testing).
     - `server`: `"java"` (indicating the server type used by WIRIS).
   - If your backend (e.g., Django) hosts a WIRIS service, you can use a local endpoint instead, such as `http://127.0.0.1:8000/pluginwiris_engine/app/configurationjs`.

5. **Remove Conflicting Configurations**:
   - Your original setup included a `mathjax` configuration in the `init` object. TinyMCE 6 does not have a built-in MathJax plugin, and this setting is not functional with WIRIS. Since WIRIS handles equation editing, remove the `mathjax` configuration to avoid confusion.
   - If you need MathJax for other parts of your application (e.g., in a separate `FormulaField` component), you can manage it independently.

---

#### Step 5: Handle Script Loading to Avoid Errors
The `Unexpected token '<'` error you encountered previously occurs when the browser expects a JavaScript file (e.g., `plugin.min.js`) but receives an HTML response, typically due to a 404 error (file not found) or server misconfiguration. To prevent this, ensure proper script loading.

1. **Verify File Existence**:
   - Confirm that `public/wiris/plugin.min.js` exists after copying it. If the file is missing, TinyMCE will fail to load the WIRIS plugin, and the server may return a 404 error page (HTML), causing the `Unexpected token '<'` error.
   - Manually navigate to `http://localhost:3001/wiris/plugin.min.js` in your browser to ensure it loads JavaScript content, not an HTML error page.

2. **Use a CDN Fallback (Optional)**:
   - If the local WIRIS plugin fails to load, you can use the WIRIS CDN as a fallback.
   - Update the `external_plugins` setting to point to the WIRIS CDN URL: `https://www.wiris.net/demo/plugins/tinymce/plugin.js`.
   - This ensures that the plugin loads even if the local file is unavailable, though it relies on an internet connection and may be slower.

3. **Dynamic Script Loading (Optional)**:
   - To make the integration more robust, you can dynamically load the WIRIS plugin script in a `useEffect` hook before rendering the `Editor` component.
   - Steps:
     - Create a state variable (e.g., `isWirisLoaded`) to track whether the WIRIS script has loaded.
     - In a `useEffect` hook, create a `<script>` element, set its `src` to `/wiris/plugin.min.js`, and append it to the document body.
     - Add `onload` and `onerror` handlers to the script element to update the state and handle loading failures.
     - Conditionally render the `Editor` component only when the script is loaded, displaying a "Loading..." message otherwise.
   - This approach ensures that TinyMCE doesn’t attempt to load the WIRIS plugin until the script is available, reducing the likelihood of errors.

---

#### Step 6: Test the Integration
After updating your component, test the integration to ensure the WIRIS Formula Editor works as expected.

1. **Start Your Development Server**:
   - Run your React application to start the development server.
   **Command**:
   ```bash
   npm start
   ```
   - This typically starts the server at `http://localhost:3001`.

2. **Add a Rich Text Editor Field**:
   - In your form, click the "ADD RICH TEXT EDITOR" button to render a TinyMCE editor.
   - The toolbar should now include the WIRIS buttons:
     - A math formula button (often shown as a sigma (Σ) or square root icon).
     - A chemistry formula button (often shown as a flask or molecule icon).

3. **Test the Formula Editor**:
   - Click the math formula button to open the WIRIS editor.
   - Create a simple equation (e.g., `x^2 + y^2`) and insert it into the TinyMCE editor. It should appear as a formatted equation (e.g., as an image or MathML).
   - Repeat the process with the chemistry formula button to create a chemical equation (e.g., `H_2O`).

4. **Check for Errors**:
   - Open your browser’s developer tools (F12) and go to the Console tab. Ensure there are no `Unexpected token '<'` errors.
   - Go to the Network tab and verify the following requests:
     - `http://localhost:3001/wiris/plugin.min.js`: Should return a 200 status with JavaScript content.
     - `https://www.wiris.net/demo/plugins/app/configurationjs`: Should return a 200 status (this is the WIRIS service endpoint).

---

#### Step 7: Debug Common Issues
If the WIRIS buttons don’t appear or you encounter errors, here’s how to troubleshoot:

1. **WIRIS Buttons Not Visible**:
   - Ensure `"tiny_mce_wiris"` is included in the `plugins` array and the `toolbar` string includes `tiny_mce_wiris_formulaEditor` and `tiny_mce_wiris_formulaEditorChemistry`.
   - Verify that the WIRIS plugin script loaded successfully in the Network tab.

2. **Unexpected token '<' Error**:
   - This error occurs if `http://localhost:3001/wiris/plugin.min.js` returns an HTML error page (e.g., 404).
   - Confirm that `public/wiris/plugin.min.js` exists.
   - If the file is missing, re-run the copy command:
     ```bash
     cp node_modules/@wiris/mathtype-tinymce6/plugin.min.js public/wiris/plugin.min.js
     ```
   - Alternatively, switch to the WIRIS CDN in `external_plugins`:
     - URL: `https://www.wiris.net/demo/plugins/tinymce/plugin.js`.

3. **WIRIS Service Endpoint Fails**:
   - If the request to `https://www.wiris.net/demo/plugins/app/configurationjs` fails, WIRIS won’t initialize.
   - Check your internet connection (since this is an external URL).
   - If your backend provides a WIRIS service, update `mathTypeParameters` to use the local endpoint (e.g., `http://127.0.0.1:8000/pluginwiris_engine/app/configurationjs`).

4. **Clear Cache**:
   - If changes don’t take effect, clear your browser cache or test in an incognito window.
   - You can also clear the `node_modules` cache and reinstall dependencies:
     ```bash
     rm -rf node_modules package-lock.json
     npm install
     npm start
     ```

---

#### Step 8: Enhance the Integration (Optional)
Once the WIRIS Formula Editor is working, you can enhance the integration:

1. **Customize the WIRIS Editor**:
   - Use `mathTypeParameters` to customize the WIRIS editor, such as setting the language:
     - Example: Add `editorParameters: { language: "en" }` to `mathTypeParameters`.
   - This ensures the WIRIS popup editor uses English, which can be helpful for consistency.

2. **Add More Toolbar Buttons**:
   - You can add more TinyMCE buttons to the toolbar for additional functionality, such as `alignleft aligncenter alignright` for text alignment or `bullist numlist` for lists.
   - Ensure the corresponding plugins are included in the `plugins` array (e.g., `lists` for `bullist` and `numlist`).

3. **Handle Formula Rendering Outside TinyMCE**:
   - Your form also has a `FormulaField` component for standalone formula fields. If `FormulaField` uses MathJax for rendering, ensure MathJax is loaded separately in your app (e.g., via a CDN script in `index.html`).
   - Since WIRIS handles formulas inside TinyMCE, you don’t need MathJax in the editor itself.

---

#### Commands Used in the Process
Here’s a summary of the commands used throughout the integration:

1. **Install TinyMCE Packages**:
   ```bash
   npm install @tinymce/tinymce-react tinymce
   ```

2. **Copy TinyMCE to Public Folder** (optional, if not using CDN):
   ```bash
   cp -r node_modules/tinymce public/tinymce
   ```

3. **Install WIRIS Plugin**:
   ```bash
   npm install @wiris/mathtype-tinymce6
   ```

4. **Copy WIRIS Plugin to Public Folder**:
   ```bash
   mkdir -p public/wiris
   cp node_modules/@wiris/mathtype-tinymce6/plugin.min.js public/wiris/plugin.min.js
   ```

5. **Start the Development Server**:
   ```bash
   npm start
   ```

6. **Clear Cache and Reinstall Dependencies** (if needed):
   ```bash
   rm -rf node_modules package-lock.json
   npm install
   npm start
   ```

---

#### Conclusion
Integrating the WIRIS Formula Editor into a TinyMCE editor in a React application involves installing the necessary packages, setting up the WIRIS plugin, and configuring TinyMCE to load the plugin and display its formula editor buttons in the toolbar. By following the steps above, you can enable users to create and edit mathematical and chemical equations directly within the rich text editor, enhancing the functionality of your form for academic or scientific use. The process includes careful handling of script loading to avoid common errors like `Unexpected token '<'`, and testing ensures that the integration works seamlessly.

If you encounter any issues during implementation, you can debug by checking the browser’s Console and Network tabs, verifying file paths, and using fallback options like the WIRIS CDN. This integration provides a powerful tool for users to include formatted equations in their content, making your application more suitable for research paper creation and similar use cases.

--- 

Let me know if you need further clarification or additional details!