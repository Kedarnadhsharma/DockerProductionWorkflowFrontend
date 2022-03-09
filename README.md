# Continuous Integration and Deployment to AWS.

This is a simple React App which is deplyoed as Docker Container in AWS Elastic Beanstack. The following sections lists the step by step process of setting up a Continuous Integration Process from local desktop/laptop. For this project TravisCI is used as a the tool for performing Continuous Integration and Deployment to AWS. A seperate Docker Container is also used to run the default tests that are created as a part of the default bootstrapped React Project.


## Prerequisites

1. React JS Library
2. Docker Desktop
3. Travis CI Account
4. GitHub
5. AWS Account

### `React Application`

1. Create a simple React App by running npx create-react-app in the command prompt and make sure you can view it by  [http://localhost:3000](http://localhost:3000) to view it in your browser.

### `Add Container Support to React`
3. Create two Docker files one for Development (Dockerfile.dev) and another for Production version (Dockerfile) of the React Application as below.
The only difference between these two is, for Production Docker container, we use NGINX as the webserver to host the React Application and also uses the multi stage build to copy the build output to NGINX.

a.  Dockerfile.dev

   ![image](https://user-images.githubusercontent.com/50028950/157384321-a72fb287-4ceb-4b31-9fdc-7644e2b63431.png)

b. Dockerfile (Production)
    
   ![image](https://user-images.githubusercontent.com/50028950/157384669-d6fee228-6c78-47ea-99e5-38edef76c1ed.png)
   
c. We can run these containers using the docker build and docker run commands using the port mapping and confirm there are no issues in the Docker configuration.

  ![image](https://user-images.githubusercontent.com/50028950/157386251-3e0469d0-5d03-400d-9b5f-f361b3fb2288.png)

d. Run docker run -p 9000:3000 -t ktadikonda/dockerreactaws and navigate to http://localhost:9000. 
  
  ![image](https://user-images.githubusercontent.com/50028950/157387568-234ca600-7dd4-4bc0-96bd-a287dba0cd41.png)
  
e. To create the Production container, we just run the docker build command without the -f parameter. In this case, Docker will use the default Dockefile that we created for Production in above will be used to create the container. I used the tag name "dockerreactawsprod" to differentiate it from existing development image from above step. 

  ![image](https://user-images.githubusercontent.com/50028950/157388126-feee0a74-23a4-4bc6-872d-32195d88c110.png)



The page will reload when you make changes.\
You may also see any lint errors in the console.

### `npm test`

Launches the test runner in the interactive watch mode.\
See the section about [running tests](https://facebook.github.io/create-react-app/docs/running-tests) for more information.

### `npm run build`

Builds the app for production to the `build` folder.\
It correctly bundles React in production mode and optimizes the build for the best performance.

The build is minified and the filenames include the hashes.\
Your app is ready to be deployed!

See the section about [deployment](https://facebook.github.io/create-react-app/docs/deployment) for more information.

### `npm run eject`

**Note: this is a one-way operation. Once you `eject`, you can't go back!**

If you aren't satisfied with the build tool and configuration choices, you can `eject` at any time. This command will remove the single build dependency from your project.

Instead, it will copy all the configuration files and the transitive dependencies (webpack, Babel, ESLint, etc) right into your project so you have full control over them. All of the commands except `eject` will still work, but they will point to the copied scripts so you can tweak them. At this point you're on your own.

You don't have to ever use `eject`. The curated feature set is suitable for small and middle deployments, and you shouldn't feel obligated to use this feature. However we understand that this tool wouldn't be useful if you couldn't customize it when you are ready for it.

