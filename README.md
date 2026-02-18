🛰️ DGPS Basemap & HMM Map-Matching Framework
Real-Time Traffic Congestion Monitoring System - Chennai Police
This repository contains a specialized geospatial pipeline to transform raw, high-precision DGPS (Differential Global Positioning System) survey data into a topological road network, and subsequently align noisy vehicle trajectories to that network using Hidden Markov Models (HMM).

📂 Project Workflow & File Structure
The project is executed in two distinct phases: Basemap Construction and Map-Matching Execution.

Phase 1: High-Precision Basemap Building
Script for base map building.ipynb: The entry point for raw survey data.

Input: Raw DGPS Excel file (e.g., Path wise sorted DGPS Kotturpuram.xlsx).

Logic: Converts Easting/Northing (UTM Zone 44N) into WGS84 coordinates, preserves survey metadata (DES column), and builds a directed graph with from_id, to_id, and native bearing calculations.

Outputs: base_map_nodes.shp and base_map_edges.shp.

Phase 2: Map-Matching (MM) Algorithms
Three levels of algorithms are provided, ranging from basic snapping to advanced probabilistic modeling:

1) MM_Topological algo.ipynb:

Basic: Snaps GPS points to the single nearest road segment. High risk of "road jumping" in dense areas.

2) MM_weight_DGPS.ipynb:

Intermediate: Uses a composite score of Spatial Distance + Heading Agreement to select the best road.

3) MM_HMM_For_DGPS.ipynb:

Advanced: The primary research algorithm. Uses a Hidden Markov Model and the Viterbi algorithm to find the most probable path across a sequence of points.

Phase 3: Visualization & Validation
Animation of GPS Coordinates.ipynb: Converts the final output into an interactive HTML animation using folium. Allows researchers to play back the "matched" path of the vehicle to verify algorithm performance.

Phase 1: DGPS Data Requirements
The Basemap Building script requires a specific format to ensure topological integrity:

Mandatory Columns:

Easting & Northing: Raw metric coordinates from the DGPS station.

ID: An incremental integer defining the physical sequence of the road survey.

DES: Metadata descriptions (e.g., E1GM, BASESESTN) used to identify road types or landmarks.

Transformation: The script automatically projects these from UTM Zone 44N (EPSG:32644) to WGS84 (EPSG:4326) for compatibility with standard GPS data.

Phase 2: Detailed Analysis of the HMM Algorithm
The HMM Map-Matching (3) MM_HMM_For_DGPS.ipynb) is the core of this research. It is designed to overcome the limitations of standard GPS noise in urban canyons.

How it Works:
Emission Probability: For every noisy GPS point, the algorithm identifies all possible road candidates within a 50m Search Radius. It calculates the likelihood of the vehicle being on that road based on a Gaussian distribution of the distance.

Transition Probability: Unlike basic snappers, HMM asks: "Is it physically possible to move from the previous road to this one?" High weights are given to connected segments in the DGPS basemap; near-zero weights are given to "teleporting" between parallel, unconnected roads.

Viterbi Decoding: The algorithm builds a Trellis Graph of all possibilities and solves for the "Shortest Path" (Maximum Likelihood Path). This ensures the vehicle trajectory remains smooth and topologically sound, even if the GPS signal drifts significantly.

Outputs & Visualization
Algorithm Outputs:
The final processed file (e.g., final_hmm_commercial_grade.csv) includes:

Original Latitude/Longitude.

hmm_matched_lat / hmm_matched_lon: The high-accuracy coordinates snapped precisely to the DGPS road segments.

matched_segment_id: The exact DGPS edge ID, allowing the Chennai Police to identify exactly which road segment is experiencing congestion.

Visualization:
The animation script produces an HTML file (e.g., Dec6_Kotturpuram_animation.html). It uses TimestampedGeoJson to provide a play/pause interface, showing the vehicle's movement along the DGPS-defined path
