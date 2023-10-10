const express = require("express");
const path = require("path");
const dbPath = path.join(__dirname, "userData.db");
const { open } = require("sqlite");
const sqlite3 = require("sqlite3");
const app = express();
const db = null;
const bcrypt = require("bcrypt");
app.use(express.json());

const initializationDbAndServer = async () => {
  try {
    db = await open({
      filename: dbPath,
      driver: sqlite3.Database,
    });
    app.listen(3000, () => {
      console.log("Server Running at http://localhost:3000/");
    });
  } catch (error) {
    console.log(`DB Error:${error.message}`);
    process.exit(1);
  }
};

initializationDbAndServer();

const validPassword = (password) => {
  return password.length > 4;
};

//register

app.post("/register", async (request, response) => {
  const { username, name, password, gender, location } = request.body;
  const hashedPassword = await bcrypt.hash(password, 10);
  const getQuery = `
    SELECT
        *
    FROM
        user
    WHERE
        username = '${username}';`;
  const dbUser = await db.get(getQuery);
  if (dbUser === undefined) {
    createQuery = `
        INSERT INTO
          user(username,name,password,gender,location)
        VALUES
          (
              '${username}',
              '${name}',
              '${hashedPassword}',
              '${gender}',
              '${location}'
          )`;
    if (validPassword(password)) {
      await db.run(createQuery);
      respond.send("User created successfully");
    } else {
      response.status(400);
      response.send("Password is too short");
    }
  } else {
    response.status(400);
    response.send("User already exists");
  }
});

//login

app.post("/login", async (request, response) => {
  const { username, password } = request.body;
  const loginQuery = `
    SELECT
        *
    FROM
        user
    WHERE
        username = '${username}';`;
  dbResponse = await db.get(loginQuery);
  if (dbResponse === undefined) {
    response.status(400);
    response.send("Invalid user");
  } else {
    const isPasswordMatched = await bcrypt.compare(
      password,
      dbResponse.password
    );
    if (isPasswordMatched === true) {
      response.send("Login success!");
    } else {
      response.status(400);
      response.send("Invalid password");
    }
  }
});

//put

app.put("/change-password", async (request, response) => {
  const { username, oldPassword, newPassword } = request.body;
  const modifiedQuery = `
    SELECT
       *
    FROM
       user
    WHERE
        username = '${username}';`;
  dbUserResponse = await db.get(modifiedQuery);
  if (dbUserResponse === undefined) {
    response.status(400);
    response.send("Invalid User");
  } else {
    const isPasswordMatched = await bcrypt.compare(
      oldPassword,
      dbUserResponse.password
    );
    if (isPasswordMatched === true) {
      if (validPassword(newPassword)) {
        const hashedPassword = await bcrypt.hash(oldPassword, 10);
        const putQuery = `
                UPDATE
                     user
                SET
                   password = '${hashedPassword}';`;
        await db.run(putQuery);
        response.send("Password updated");
      } else {
        response.status(400);
        response.send("Password is too short");
      }
    } else {
      response.status(400);
      response.send("Invalid current password");
    }
  }
});

module.exports = app;
