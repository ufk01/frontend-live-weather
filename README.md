
# How To Run Frontend

## Project Setup

1. **Install Dependencies**
   ```bash
   npm install
   ```

2. **Compile and Hot-Reload for Development**
   ```bash
   npm run serve
   ```

3. **Compile and Minify for Production**
   ```bash
   npm run build
   ```

4. **Lint and Fix Files**
   ```bash
   npm run lint
   ```


## Project Structure

- The project is developed using JavaScript (Vue.js).
- It is a single-page application, with the main code written directly in `App.vue`.
- Additionally, a separate component has been added for popups.
- The frontend continuously displays real-time results to the user.
- The data table shows the latest 5 entries.
- Default data (Turkey/Ankara) is retrieved from the backend configuration (.yml) file. Other input fields are processed based on user input.

