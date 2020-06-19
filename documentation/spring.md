# Driver

We have already mention the concept driver in [Main concepts](getting-started/main-concepts.md#driver). Drivers are the part of Mongock in charge of dealing with MongoDB.

### Motivation

In order to provide support to different MongoDB versions, Java MongoDB drivers and libraries, we need to separate this connector from the rest of Mongock, so the user can choose the right alternative for him\(for example, using directly the Java MongoDB driver or Spring Data library\)

It's also important when comes to old version. Users may don't want to upgrade to the last version of MongoDB or driver, but they still need Mongock to be supported for their production deployments.

### How it works

Technically drivers are simple implementations of the same interface which provides the contract between the driver itself and the [runner](standalone.md).

The idea is to provide a specific driver for every Java MongoDB driver, library or need. Open source users will be able to add drivers, following the driver specification.

### Driver types

Currently Mongock provides the following drivers:

<table>
  <thead>
    <tr>
      <th style="text-align:left">Mongock driver</th>
      <th style="text-align:left">Java library</th>
      <th style="text-align:left">Mongock Runners compatible</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>mongodb-v3-driver</b>
      </td>
      <td style="text-align:left">org.mongodb: mongo.java.driver: 3.11.0</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone:4.x</li>
          <li>mongock-spring-v5</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongodb-sync-v4-driver</b>
      </td>
      <td style="text-align:left">org.mongodb: mongodb-driver-sync: 4.0.2</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone: 4.x</li>
          <li>mongock-spring-v5: 4.x</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongodb-springdata-v2-driver</b>
      </td>
      <td style="text-align:left">org.springframework.data: spring-data-mongodb: 2.2.4.RELEASE</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone: 4.x</li>
          <li>mongock-spring-v5: 4.x</li>
        </ul>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>mongodb-springdata-v3-driver</b>
      </td>
      <td style="text-align:left">org.springframework.data: spring-data-mongodb: 3.0.0.RELEASE</td>
      <td style="text-align:left">
        <p></p>
        <ul>
          <li>mongock-standalone: 4.x</li>
          <li>mongock-spring-v5: 4.x</li>
        </ul>
      </td>
    </tr>
  </tbody>
</table>



