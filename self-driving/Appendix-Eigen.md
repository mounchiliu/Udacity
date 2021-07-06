### Notes for using the Eigen Library:
- you can create a vertical vector of two elements with a command like this:

  VectorXd my_vector(2);

- if the vector size was previously specified, you can use the so-called comma initializer to set all the coefficients to some values:

  my_vector << 10, 20;

- you can use the cout command to print out the vector:

  cout << my_vector << endl;
- the matrices can be created in the same way
  for example, this is an initialization of a 2 by 2 matrix with the values 1, 2, 3, and 4:
  '' MatrixXd my_matrix(2,2);
     my_matrix << 1, 2,
             3, 4;
             ''
- you can use the same comma initializer or you can set each matrix value explicitly
  for example, that's how we can change the matrix elements in the second row:
  '' my_matrix(1,0) = 11;    //second row, first column
      my_matrix(1,1) = 12;    //second row, second column
  ''
- you can compute the transpose of a matrix with the following command:
  MatrixXd my_matrix_t = my_matrix.transpose();
- you can get the matrix inverse with:
  MatrixXd my_matrix_i = my_matrix.inverse();
- for multiplying the matrix m* with the vector *b you can write this in one line as let’s say matrix c* equals *m times v:
  ''
  MatrixXd another_matrix;
  another_matrix = my_matrix*my_vector;
  ''
