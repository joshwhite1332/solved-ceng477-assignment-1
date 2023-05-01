Download Link: https://assignmentchef.com/product/solved-ceng477-assignment-1
<br>
<h1>1          Objectives</h1>

In this assignment, you are going to implement Modeling Transformation, Viewing Transformation, and Rasterization stages of the Forward Rendering Pipeline. Specifically, given a set of triangles and the position and the color attributes of all of their vertices, your goal is to render the scene as a two-dimensional (2D) image. The parameters of a perspective view camera will be specified similar to the first assignment. You will transform all of the vertices to the viewport and then use line drawing and triangle rasterization algorithms to display the triangles in wireframe or solid modes. You will implement a clipping algorithm of your choice <strong>(Cohen-Sutherland or Liang-Barsky) </strong>for wireframe mode only. You will also implement the backface culling (for both modes) for correct rendering of the scene. You will write your implementations in C++ language.

<strong>Keywords: </strong><em>forward rendering pipeline, modeling transformations, viewing transformations, line drawing, triangle rasterization, interpolation, clipping, backface culling</em>

<h1>2          Specifications</h1>

<ol>

 <li>Name of the executable will be “rasterizer”.</li>

 <li>Your executable will take an input file name from command line. Input file names are not static, they can be anything.</li>

 <li>The scene will be composed of instances of meshes. A set of triangles will form a mesh with a sequence of several transformations (translation, rotation, scaling) you will be able to move, rotate, or resize a mesh (i.e., all of the triangles in the mesh). Transformations will be applied to the meshes in the order specified in the input file.</li>

 <li>You will not implement any lighting computations in this assignment. The color of each vertex will be provided as input and your goal will be to interpolate color along the edges and the surfaces of the triangles in wireframe and solid modes, respectively.</li>

 <li>You will implement two types of projection transformations: <strong>orthographic projection and perspective projection</strong>.</li>

 <li>Use the midpoint algorithm to draw triangle edges and use the barycentric coordinates based algorithm to rasterize triangle faces.</li>

 <li>In both wireframe and solid modes, triangles whose backface is facing the viewer will be culled. Backface culling should be enabled/disabled according to the setting in input file. You should calculate normal vectors for backface culling. <strong>Default value for backface culling setting is ’disabled’.</strong></li>

 <li><strong>You will also implement a clipping algorithm of your choice. </strong>For example, you can choose an algorithm among algorithms that you saw on the lectures. You will do clipping for wireframe mode only. For solid mode meshes, you can assume that the mesh is completely visible to the camera.</li>

 <li>There can be multiple camera configurations for each given scene.</li>

 <li><strong>You will NOT deal with reading input files. </strong>Helper functions for reading inputs, helper functions for mathematical operations (e.g. normalization, matrix multiplication) are given to you in <strong>“Scene.cpp” </strong>and <strong>“Helpers.cpp” </strong> It is <strong>STRONGLY </strong>recommended to inspect types in header files and helper functions, which are given to you in homework stub code. You can modify these helper functions as you wish, or you can implement your own helper functions.</li>

 <li><strong>Last important note: You will NOT implement depth buffer algorithm in this homework. </strong>Meshes will be given to you in back-to-front order. So, when you draw a mesh, you can assume that next mesh is closer to the camera than previous meshes.</li>

</ol>

<h1>3          Input File</h1>

You will not deal with reading input files, however, it is good for you to know what input file contains for testing purposes. Input files are in XML format and their structure is given below:

&lt;Scene&gt;

&lt;BackgroundColor&gt;R G B&lt;/BackgroundColor&gt;

&lt;Culling&gt; enabled or disabled &lt;/Culling&gt;

&lt;Cameras&gt;

&lt;Camera id=”#” type=”perspective or orthographic”&gt;

&lt;Position&gt;X Y Z&lt;/Position&gt;

&lt;Gaze&gt;X Y Z&lt;/Gaze&gt;

&lt;Up&gt;X Y Z&lt;/Up&gt;

&lt;ImagePlane&gt;

Left Right Bottom Top Near Far HorRes VerRes

&lt;/ImagePlane&gt;

&lt;OutputName&gt; &lt;image_name&gt;.ppm &lt;/OutputName&gt;

&lt;/Camera&gt;

… more cameras …

&lt;/Cameras&gt;

&lt;Vertices&gt;

&lt;Vertex id=”1″ position=”X Y Z” color=”R G B” /&gt; … more vertices …

&lt;/Vertices&gt;

&lt;Translations&gt;

&lt;Translation id=”1″ value=”tx ty tz” /&gt; … more translations …

&lt;/Translations&gt;

&lt;Scalings&gt;

&lt;Scaling id=”1″ value=”sx sy sz” /&gt; … more scalings …

&lt;/Scalings&gt;

&lt;Rotations&gt;

&lt;Rotation id=”1″ value=”angle ux uy uz” /&gt; … more rotations …

&lt;/Rotations&gt;

&lt;Meshes&gt;

&lt;Mesh id=”#” type=”wireframe or solid”&gt;

&lt;Transformations&gt;

&lt;Transformation&gt; &lt;transformation_type&gt; &lt;transformation_id&gt; &lt;/Transformation&gt; … more transformations …

&lt;/Transformations&gt;

&lt;Faces&gt;

&lt;vertex_id_1_triangle_1&gt; &lt;vertex_id_2_triangle_1&gt; &lt;vertex_id_3_triangle_1&gt;

&lt;vertex_id_1_triangle_2&gt; &lt;vertex_id_2_triangle_2&gt; &lt;vertex_id_3_triangle_2&gt; … more faces (triangles) …

&lt;/Faces&gt;

&lt;/Mesh&gt;

… more meshes …

&lt;/Meshes&gt;

&lt;/Scene&gt;

<h2>3.1        Input File Sections</h2>

<strong>3.1.1      Background color:</strong>

Specifies the R, G, B values of the background.

<strong>3.1.2       Backface Culling Setting:</strong>

Specifies whether culling is applied or not. <strong>If it is ’disabled’, there will be no culling</strong>, just draw all triangles. <strong>If it is ’enabled’, culling should be done when triangle’s backface is facing to the viewer.</strong>

<strong>3.1.3      Cameras:</strong>

<ul>

 <li><strong>Id </strong>specifies the camera id.</li>

 <li><strong>Type </strong>specifies which projection transformation is applied. <strong>If it is ’orthographic’, that means you need to apply orthographic projection</strong>, <strong>If it is ’perspective’, you should apply perspective projection.</strong></li>

 <li><strong>Position </strong>specifies the X, Y, Z coordinates of the camera.</li>

 <li><strong>Gaze </strong>specifies the direction that the camera is looking at.</li>

 <li><strong>Up </strong>specifies the up vector of the camera. The up vector is not necessarily given as orthogonal to the gaze vector. Therefore the camera’s x-axis is found by a cross product of the gaze and up vectors, then the up vector is corrected by a cross product of gaze and x-axis vectors of the camera.</li>

 <li><strong>ImagePlane </strong>specifies the coordinates of the image plane in <strong>Left</strong>, <strong>Right</strong>, <strong>Bottom</strong>, <strong>Top </strong>parameters; distance of the image plane to the camera in <strong>Near </strong>and distance of the far plane to the camera in <strong>Far </strong>parameters, and the resolution of the final image in <strong>HorRes </strong>and <strong>VerRes </strong> All values are floats except <strong>HorRes </strong>and <strong>VerRes</strong>, which are integers.</li>

 <li><strong>OutputName </strong>is a string which is the name of the output image.</li>

</ul>

<strong>3.1.4     Vertices:</strong>

<ul>

 <li><strong>Id </strong>specifies the vertex id.</li>

 <li><strong>Position </strong>specifies the position of the vertex in X Y Z.</li>

 <li><strong>Color </strong>specifies the color of each vertex in R G B.</li>

</ul>

<strong>3.1.5     Translations:</strong>

<ul>

 <li><strong>Id </strong>specifies the translation id.</li>

 <li><strong>Value </strong>specifies <em>t<sub>x</sub></em>, <em>t<sub>y</sub></em>, and <em>t<sub>z</sub></em>, which are the translation parameters, i.e., translation amounts along the major axes.</li>

</ul>

<strong>3.1.6     Scalings:</strong>

<ul>

 <li><strong>Id </strong>specifies the scaling id.</li>

 <li><strong>Value </strong>specifies <em>s<sub>x</sub></em>, <em>s<sub>y</sub></em>, and <em>s<sub>z</sub></em>, which are the scaling parameters, i.e., scaling level in the corresponding coordinate axes.</li>

</ul>

<strong>3.1.7      Rotations:</strong>

<ul>

 <li><strong>Id </strong>specifies the rotation id.</li>

 <li><strong>Value </strong>specifies <em>α</em>, <em>u<sub>x</sub></em>, <em>u<sub>y</sub></em>, and <em>u<sub>z</sub></em>, which are the rotation parameters, i.e., the object is rotated <em>α </em>degrees around the rotation axis which pass through points (<em>u<sub>x</sub>,u<sub>y</sub>,u<sub>z</sub></em>) and (0<em>,</em>0<em>,</em>0). The positive angle of rotation is given as the counter-clockwise rotation along the direction (<em>u<sub>x</sub>,u<sub>y</sub>,u<sub>z</sub></em>).</li>

</ul>

<strong>3.1.8     Meshes:</strong>

<ul>

 <li><strong>Id </strong>specifies the mesh id.</li>

 <li><strong>Type </strong>specifies the type of the mesh. <strong>’wireframe’ for wireframe, ’solid’ for solid rendering.</strong></li>

 <li>Each transformation has transformation type and transformation id parameters. The ‘t’ indicates a translation transformation, ‘s’ indicates a scale transformation, and ‘r’ indicates a rotation transformation. The transformation id ranges from [1 …translation count] for type ‘t’ transformations, and similarly for the other type of transformations.</li>

 <li>In <strong>Faces </strong>section you can find the triangles and each line represents a triangle. Each triangle is given as a list of vertex ids in counter clockwise order when faced from the front side. Vertex ids start from 1.</li>

</ul>

<h1>4          Sample input/output</h1>

A sample camera and a scene file are provided below:

&lt;Scene&gt;

&lt;BackgroundColor&gt;255 255 255&lt;/BackgroundColor&gt;

&lt;Culling&gt;enabled&lt;/Culling&gt;    &lt;——- backface culling is enabled &lt;Cameras&gt;

&lt;Camera id=”1″ type=”perspective”&gt; &lt;——- perspective projection

&lt;Position&gt;-10 -40 -26&lt;/Position&gt;

&lt;Gaze&gt;1 1 -0.2&lt;/Gaze&gt;

&lt;Up&gt;0 1 0&lt;/Up&gt;

&lt;ImagePlane&gt;-1 1 -1 1 2 1000 700 700&lt;/ImagePlane&gt;

&lt;OutputName&gt;filled_box_1.ppm&lt;/OutputName&gt;

&lt;/Camera&gt;

&lt;Camera id=”2″ type=”perspective”&gt; &lt;——- perspective projection

&lt;Position&gt;-20 -28 -23&lt;/Position&gt;

&lt;Gaze&gt;1 0.5 -0.2&lt;/Gaze&gt;

&lt;Up&gt;0 1 0&lt;/Up&gt;

&lt;ImagePlane&gt;-1 1 -1 1 2 1000 700 700&lt;/ImagePlane&gt;

&lt;OutputName&gt;filled_box_2.ppm&lt;/OutputName&gt;

&lt;/Camera&gt;

&lt;/Cameras&gt;

&lt;Vertices&gt;

&lt;Vertex id=”1″ position=”1.0 1.0 -1.0″ color=”100 100 100″ /&gt;

&lt;Vertex id=”2″ position=”-1.0 1.0 -1.0″ color=”255 0 0″ /&gt;

&lt;Vertex id=”3″ position=”-1.0 1.0 1.0″ color=”0 255 0″ /&gt;

&lt;Vertex id=”4″ position=”1.0 1.0 1.0″ color=”0 0 255″ /&gt;

&lt;Vertex id=”5″ position=”1.0 -1.0 -1.0″ color=”0 0 255″ /&gt;

&lt;Vertex id=”6″ position=”-1.0 -1.0 -1.0″ color=”0 255 0″ /&gt;

&lt;Vertex id=”7″ position=”-1.0 -1.0 1.0″ color=”255 0 0″ /&gt;

&lt;Vertex id=”8″ position=”1.0 -1.0 1.0″ color=”100 100 100″ /&gt;

&lt;/Vertices&gt;

&lt;Translations&gt;

&lt;Translation id=”1″ value=”0.0 10.0 0.0″ /&gt;

&lt;Translation id=”2″ value=”3.0 -3.0 -6.0″ /&gt;

&lt;/Translations&gt;

&lt;Scalings&gt;

&lt;Scaling id=”1″ value=”5.2 5.2 5.2″ /&gt;

&lt;/Scalings&gt;

&lt;Rotations&gt;

&lt;Rotation id=”1″ value=”45 0.0 1.0 0.0″ /&gt;

&lt;Rotation id=”2″ value=”60 0.8 0.6 0.0″ /&gt;

&lt;Rotation id=”3″ value=”20 1.0 0.0 0.0″ /&gt;

&lt;/Rotations&gt;

&lt;Meshes&gt;

&lt;Mesh id=”1″ type=”solid”&gt; &lt;——- mesh is drawn in solid mode &lt;Transformations&gt;

&lt;Transformation&gt;r 1&lt;/Transformation&gt;

&lt;Transformation&gt;t 2&lt;/Transformation&gt;

&lt;Transformation&gt;s 1&lt;/Transformation&gt;

&lt;/Transformations&gt;

&lt;Faces&gt;

7 8 4

<ul>

 <li>4 3</li>

 <li>5 1</li>

</ul>

8 1 4

6 3 2

6 7 3

3 4 1

3 1 2

6 2 5

2 1 5

5 8 6

7 6 8

&lt;/Faces&gt;

&lt;/Mesh&gt;

&lt;/Meshes&gt;

&lt;/Scene&gt;

You can see outputs of this example in the next page.

(a) Solid-culling enabled                   (b) Solid-culling disabled

(c) Wireframe-culling enabled      (d) Wireframe-culling disabled

Figure 1: Same mesh, different modes of rendering

<h1>5          Hints &amp; Tips</h1>

<ol>

 <li>Start early!</li>

 <li>Note that in the <strong>wireframe </strong>mode <strong>only the edges of the triangle will be drawn</strong>.</li>

 <li>For debugging purposes, consider using simple scenes. Also it may be necessary to debug your code by tracing what happens for a single triangle (always simplify the problem when debugging).</li>

 <li>You can reach different inputs in <strong>“culling disabled inputs” </strong>and <strong>“culling enabled inputs” </strong>folders and desired images in <strong>“culling disabled outputs”</strong>, <strong>“culling enabled outputs” </strong>and <strong>“different </strong><strong>projection type” </strong> You will see that different camera angles, output of different rendering modes are provided to you to understand 3d scene and rendering modes completely.</li>

</ol>

<h1>6          Regulations</h1>

<ol>

 <li><strong>Programming Language: </strong>C++</li>

 <li><strong>Late Submission: </strong>You can submit your codes up to 3 days late. Each late day will be deduced from the total 7 credits for the semester. However, if you fail to submit even after 3 days, you will get 0 regardless of how many late credits you may have left. If you submit late and still get zero, you cannot claim back your late days. You must e-mail your assistants if you want your submission to not be evaluated (and therefore preserve your late day credits).</li>

 <li><strong>Cheating: We have zero tolerance policy for cheating</strong>. People involved in cheating will be punished according to the university regulations and will get 0. You can discuss algorithmic choices, but sharing code between each other or using third party code is strictly forbidden. To prevent cheating in this homework, we also compare your codes with online ray tracers and previous years’ student solutions. In case a match is found, this will also be considered as cheating. Even if you take a “part” of the code from somewhere/somebody else – this is also cheating. Please be aware that there are “very advanced tools” that detect if two codes are similar. So please do not think you can get away with by changing a code obtained from another source.</li>

 <li><strong>Newsgroup: </strong>You must follow the announcements in OdtuClass (odtuclass.metu.edu.tr) for discussions and possible updates on a daily basis.</li>

</ol>