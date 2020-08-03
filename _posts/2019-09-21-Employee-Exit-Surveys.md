---
title: "Clean and Analyze Employee Exit Surveys"
date: 2019-09-21
tags: [surveys, data cleaning]
excerpt: "Employee Exit Surveys"
mathjax: "true"
---

# Introduction

This project is working on exit surveys from the employees of the Department of Education, Training and Employment and the Technical and Further Education Institute in Queensland, Austrailia.  These are publically available datasets from data.gov.au


```python
import pandas as pd
import numpy as np

dete_survey = pd.read_csv('dete_survey.csv')
tafe_survey = pd.read_csv('tafe_survey.csv')

dete_survey.info()
dete_survey.head()



```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 822 entries, 0 to 821
    Data columns (total 56 columns):
    ID                                     822 non-null int64
    SeparationType                         822 non-null object
    Cease Date                             822 non-null object
    DETE Start Date                        822 non-null object
    Role Start Date                        822 non-null object
    Position                               817 non-null object
    Classification                         455 non-null object
    Region                                 822 non-null object
    Business Unit                          126 non-null object
    Employment Status                      817 non-null object
    Career move to public sector           822 non-null bool
    Career move to private sector          822 non-null bool
    Interpersonal conflicts                822 non-null bool
    Job dissatisfaction                    822 non-null bool
    Dissatisfaction with the department    822 non-null bool
    Physical work environment              822 non-null bool
    Lack of recognition                    822 non-null bool
    Lack of job security                   822 non-null bool
    Work location                          822 non-null bool
    Employment conditions                  822 non-null bool
    Maternity/family                       822 non-null bool
    Relocation                             822 non-null bool
    Study/Travel                           822 non-null bool
    Ill Health                             822 non-null bool
    Traumatic incident                     822 non-null bool
    Work life balance                      822 non-null bool
    Workload                               822 non-null bool
    None of the above                      822 non-null bool
    Professional Development               808 non-null object
    Opportunities for promotion            735 non-null object
    Staff morale                           816 non-null object
    Workplace issue                        788 non-null object
    Physical environment                   817 non-null object
    Worklife balance                       815 non-null object
    Stress and pressure support            810 non-null object
    Performance of supervisor              813 non-null object
    Peer support                           812 non-null object
    Initiative                             813 non-null object
    Skills                                 811 non-null object
    Coach                                  767 non-null object
    Career Aspirations                     746 non-null object
    Feedback                               792 non-null object
    Further PD                             768 non-null object
    Communication                          814 non-null object
    My say                                 812 non-null object
    Information                            816 non-null object
    Kept informed                          813 non-null object
    Wellness programs                      766 non-null object
    Health & Safety                        793 non-null object
    Gender                                 798 non-null object
    Age                                    811 non-null object
    Aboriginal                             16 non-null object
    Torres Strait                          3 non-null object
    South Sea                              7 non-null object
    Disability                             23 non-null object
    NESB                                   32 non-null object
    dtypes: bool(18), int64(1), object(37)
    memory usage: 258.6+ KB
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>SeparationType</th>
      <th>Cease Date</th>
      <th>DETE Start Date</th>
      <th>Role Start Date</th>
      <th>Position</th>
      <th>Classification</th>
      <th>Region</th>
      <th>Business Unit</th>
      <th>Employment Status</th>
      <th>...</th>
      <th>Kept informed</th>
      <th>Wellness programs</th>
      <th>Health &amp; Safety</th>
      <th>Gender</th>
      <th>Age</th>
      <th>Aboriginal</th>
      <th>Torres Strait</th>
      <th>South Sea</th>
      <th>Disability</th>
      <th>NESB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Ill Health Retirement</td>
      <td>08/2012</td>
      <td>1984</td>
      <td>2004</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>N</td>
      <td>N</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Yes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>08/2012</td>
      <td>Not Stated</td>
      <td>Not Stated</td>
      <td>Public Servant</td>
      <td>AO5-AO7</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>N</td>
      <td>N</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>05/2012</td>
      <td>2011</td>
      <td>2011</td>
      <td>Schools Officer</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>N</td>
      <td>N</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Resignation-Other reasons</td>
      <td>05/2012</td>
      <td>2005</td>
      <td>2006</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>A</td>
      <td>N</td>
      <td>A</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Age Retirement</td>
      <td>05/2012</td>
      <td>1970</td>
      <td>1989</td>
      <td>Head of Curriculum/Head of Special Education</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>A</td>
      <td>M</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 56 columns</p>
</div>




```python
dete_survey.isnull()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>SeparationType</th>
      <th>Cease Date</th>
      <th>DETE Start Date</th>
      <th>Role Start Date</th>
      <th>Position</th>
      <th>Classification</th>
      <th>Region</th>
      <th>Business Unit</th>
      <th>Employment Status</th>
      <th>...</th>
      <th>Kept informed</th>
      <th>Wellness programs</th>
      <th>Health &amp; Safety</th>
      <th>Gender</th>
      <th>Age</th>
      <th>Aboriginal</th>
      <th>Torres Strait</th>
      <th>South Sea</th>
      <th>Disability</th>
      <th>NESB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>5</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>6</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>7</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>8</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>9</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>10</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>11</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>12</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>13</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>14</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>15</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>16</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>17</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>18</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>19</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>20</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>21</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>22</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>23</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>24</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>25</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>26</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>27</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>28</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>29</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>792</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>793</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>794</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>795</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>796</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>797</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>798</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>799</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>800</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>801</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>802</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>803</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>804</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>805</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>806</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>807</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>808</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>809</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>810</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>811</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>812</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>813</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>814</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>815</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>816</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>817</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>818</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>819</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>820</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>821</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
<p>822 rows × 56 columns</p>
</div>




```python
tafe_survey.info()
tafe_survey.head()

```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 702 entries, 0 to 701
    Data columns (total 72 columns):
    Record ID                                                                                                                                                        702 non-null float64
    Institute                                                                                                                                                        702 non-null object
    WorkArea                                                                                                                                                         702 non-null object
    CESSATION YEAR                                                                                                                                                   695 non-null float64
    Reason for ceasing employment                                                                                                                                    701 non-null object
    Contributing Factors. Career Move - Public Sector                                                                                                                437 non-null object
    Contributing Factors. Career Move - Private Sector                                                                                                               437 non-null object
    Contributing Factors. Career Move - Self-employment                                                                                                              437 non-null object
    Contributing Factors. Ill Health                                                                                                                                 437 non-null object
    Contributing Factors. Maternity/Family                                                                                                                           437 non-null object
    Contributing Factors. Dissatisfaction                                                                                                                            437 non-null object
    Contributing Factors. Job Dissatisfaction                                                                                                                        437 non-null object
    Contributing Factors. Interpersonal Conflict                                                                                                                     437 non-null object
    Contributing Factors. Study                                                                                                                                      437 non-null object
    Contributing Factors. Travel                                                                                                                                     437 non-null object
    Contributing Factors. Other                                                                                                                                      437 non-null object
    Contributing Factors. NONE                                                                                                                                       437 non-null object
    Main Factor. Which of these was the main factor for leaving?                                                                                                     113 non-null object
    InstituteViews. Topic:1. I feel the senior leadership had a clear vision and direction                                                                           608 non-null object
    InstituteViews. Topic:2. I was given access to skills training to help me do my job better                                                                       613 non-null object
    InstituteViews. Topic:3. I was given adequate opportunities for personal development                                                                             610 non-null object
    InstituteViews. Topic:4. I was given adequate opportunities for promotion within %Institute]Q25LBL%                                                              608 non-null object
    InstituteViews. Topic:5. I felt the salary for the job was right for the responsibilities I had                                                                  615 non-null object
    InstituteViews. Topic:6. The organisation recognised when staff did good work                                                                                    607 non-null object
    InstituteViews. Topic:7. Management was generally supportive of me                                                                                               614 non-null object
    InstituteViews. Topic:8. Management was generally supportive of my team                                                                                          608 non-null object
    InstituteViews. Topic:9. I was kept informed of the changes in the organisation which would affect me                                                            610 non-null object
    InstituteViews. Topic:10. Staff morale was positive within the Institute                                                                                         602 non-null object
    InstituteViews. Topic:11. If I had a workplace issue it was dealt with quickly                                                                                   601 non-null object
    InstituteViews. Topic:12. If I had a workplace issue it was dealt with efficiently                                                                               597 non-null object
    InstituteViews. Topic:13. If I had a workplace issue it was dealt with discreetly                                                                                601 non-null object
    WorkUnitViews. Topic:14. I was satisfied with the quality of the management and supervision within my work unit                                                  609 non-null object
    WorkUnitViews. Topic:15. I worked well with my colleagues                                                                                                        605 non-null object
    WorkUnitViews. Topic:16. My job was challenging and interesting                                                                                                  607 non-null object
    WorkUnitViews. Topic:17. I was encouraged to use my initiative in the course of my work                                                                          610 non-null object
    WorkUnitViews. Topic:18. I had sufficient contact with other people in my job                                                                                    613 non-null object
    WorkUnitViews. Topic:19. I was given adequate support and co-operation by my peers to enable me to do my job                                                     609 non-null object
    WorkUnitViews. Topic:20. I was able to use the full range of my skills in my job                                                                                 609 non-null object
    WorkUnitViews. Topic:21. I was able to use the full range of my abilities in my job. ; Category:Level of Agreement; Question:YOUR VIEWS ABOUT YOUR WORK UNIT]    608 non-null object
    WorkUnitViews. Topic:22. I was able to use the full range of my knowledge in my job                                                                              608 non-null object
    WorkUnitViews. Topic:23. My job provided sufficient variety                                                                                                      611 non-null object
    WorkUnitViews. Topic:24. I was able to cope with the level of stress and pressure in my job                                                                      610 non-null object
    WorkUnitViews. Topic:25. My job allowed me to balance the demands of work and family to my satisfaction                                                          611 non-null object
    WorkUnitViews. Topic:26. My supervisor gave me adequate personal recognition and feedback on my performance                                                      606 non-null object
    WorkUnitViews. Topic:27. My working environment was satisfactory e.g. sufficient space, good lighting, suitable seating and working area                         610 non-null object
    WorkUnitViews. Topic:28. I was given the opportunity to mentor and coach others in order for me to pass on my skills and knowledge prior to my cessation date    609 non-null object
    WorkUnitViews. Topic:29. There was adequate communication between staff in my unit                                                                               603 non-null object
    WorkUnitViews. Topic:30. Staff morale was positive within my work unit                                                                                           606 non-null object
    Induction. Did you undertake Workplace Induction?                                                                                                                619 non-null object
    InductionInfo. Topic:Did you undertake a Corporate Induction?                                                                                                    432 non-null object
    InductionInfo. Topic:Did you undertake a Institute Induction?                                                                                                    483 non-null object
    InductionInfo. Topic: Did you undertake Team Induction?                                                                                                          440 non-null object
    InductionInfo. Face to Face Topic:Did you undertake a Corporate Induction; Category:How it was conducted?                                                        555 non-null object
    InductionInfo. On-line Topic:Did you undertake a Corporate Induction; Category:How it was conducted?                                                             555 non-null object
    InductionInfo. Induction Manual Topic:Did you undertake a Corporate Induction?                                                                                   555 non-null object
    InductionInfo. Face to Face Topic:Did you undertake a Institute Induction?                                                                                       530 non-null object
    InductionInfo. On-line Topic:Did you undertake a Institute Induction?                                                                                            555 non-null object
    InductionInfo. Induction Manual Topic:Did you undertake a Institute Induction?                                                                                   553 non-null object
    InductionInfo. Face to Face Topic: Did you undertake Team Induction; Category?                                                                                   555 non-null object
    InductionInfo. On-line Topic: Did you undertake Team Induction?process you undertook and how it was conducted.]                                                  555 non-null object
    InductionInfo. Induction Manual Topic: Did you undertake Team Induction?                                                                                         555 non-null object
    Workplace. Topic:Did you and your Manager develop a Performance and Professional Development Plan (PPDP)?                                                        608 non-null object
    Workplace. Topic:Does your workplace promote a work culture free from all forms of unlawful discrimination?                                                      594 non-null object
    Workplace. Topic:Does your workplace promote and practice the principles of employment equity?                                                                   587 non-null object
    Workplace. Topic:Does your workplace value the diversity of its employees?                                                                                       586 non-null object
    Workplace. Topic:Would you recommend the Institute as an employer to others?                                                                                     581 non-null object
    Gender. What is your Gender?                                                                                                                                     596 non-null object
    CurrentAge. Current Age                                                                                                                                          596 non-null object
    Employment Type. Employment Type                                                                                                                                 596 non-null object
    Classification. Classification                                                                                                                                   596 non-null object
    LengthofServiceOverall. Overall Length of Service at Institute (in years)                                                                                        596 non-null object
    LengthofServiceCurrent. Length of Service at current workplace (in years)                                                                                        596 non-null object
    dtypes: float64(2), object(70)
    memory usage: 395.0+ KB
    




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Record ID</th>
      <th>Institute</th>
      <th>WorkArea</th>
      <th>CESSATION YEAR</th>
      <th>Reason for ceasing employment</th>
      <th>Contributing Factors. Career Move - Public Sector</th>
      <th>Contributing Factors. Career Move - Private Sector</th>
      <th>Contributing Factors. Career Move - Self-employment</th>
      <th>Contributing Factors. Ill Health</th>
      <th>Contributing Factors. Maternity/Family</th>
      <th>...</th>
      <th>Workplace. Topic:Does your workplace promote a work culture free from all forms of unlawful discrimination?</th>
      <th>Workplace. Topic:Does your workplace promote and practice the principles of employment equity?</th>
      <th>Workplace. Topic:Does your workplace value the diversity of its employees?</th>
      <th>Workplace. Topic:Would you recommend the Institute as an employer to others?</th>
      <th>Gender. What is your Gender?</th>
      <th>CurrentAge. Current Age</th>
      <th>Employment Type. Employment Type</th>
      <th>Classification. Classification</th>
      <th>LengthofServiceOverall. Overall Length of Service at Institute (in years)</th>
      <th>LengthofServiceCurrent. Length of Service at current workplace (in years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6.341330e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Contract Expired</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6.341337e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Retirement</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6.341388e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Retirement</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>6.341399e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6.341466e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Male</td>
      <td>41  45</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 72 columns</p>
</div>




```python
tafe_survey.isnull()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Record ID</th>
      <th>Institute</th>
      <th>WorkArea</th>
      <th>CESSATION YEAR</th>
      <th>Reason for ceasing employment</th>
      <th>Contributing Factors. Career Move - Public Sector</th>
      <th>Contributing Factors. Career Move - Private Sector</th>
      <th>Contributing Factors. Career Move - Self-employment</th>
      <th>Contributing Factors. Ill Health</th>
      <th>Contributing Factors. Maternity/Family</th>
      <th>...</th>
      <th>Workplace. Topic:Does your workplace promote a work culture free from all forms of unlawful discrimination?</th>
      <th>Workplace. Topic:Does your workplace promote and practice the principles of employment equity?</th>
      <th>Workplace. Topic:Does your workplace value the diversity of its employees?</th>
      <th>Workplace. Topic:Would you recommend the Institute as an employer to others?</th>
      <th>Gender. What is your Gender?</th>
      <th>CurrentAge. Current Age</th>
      <th>Employment Type. Employment Type</th>
      <th>Classification. Classification</th>
      <th>LengthofServiceOverall. Overall Length of Service at Institute (in years)</th>
      <th>LengthofServiceCurrent. Length of Service at current workplace (in years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>2</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>4</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>7</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>8</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>10</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>11</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>12</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>13</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>14</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>15</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>16</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>17</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>18</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>19</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>20</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>21</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>22</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>23</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>24</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>25</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>26</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>27</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>28</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>29</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>672</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>673</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>674</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>675</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>676</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>677</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>678</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>679</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>680</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>681</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>682</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>683</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>684</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>685</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>686</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>687</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>688</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>689</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>690</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>691</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>692</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>693</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>694</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>695</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>696</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>697</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>698</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
    </tr>
    <tr>
      <th>699</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>700</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>True</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>701</th>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
<p>702 rows × 72 columns</p>
</div>



There are columns that are needed for analysis and missing values that need to be removed

# Identify Missing Values and Drop Unnecessary Columns


```python
dete_survey = pd.read_csv('dete_survey.csv',na_values='Not Stated')
dete_survey.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>SeparationType</th>
      <th>Cease Date</th>
      <th>DETE Start Date</th>
      <th>Role Start Date</th>
      <th>Position</th>
      <th>Classification</th>
      <th>Region</th>
      <th>Business Unit</th>
      <th>Employment Status</th>
      <th>...</th>
      <th>Kept informed</th>
      <th>Wellness programs</th>
      <th>Health &amp; Safety</th>
      <th>Gender</th>
      <th>Age</th>
      <th>Aboriginal</th>
      <th>Torres Strait</th>
      <th>South Sea</th>
      <th>Disability</th>
      <th>NESB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Ill Health Retirement</td>
      <td>08/2012</td>
      <td>1984.0</td>
      <td>2004.0</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>N</td>
      <td>N</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Yes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>08/2012</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Public Servant</td>
      <td>AO5-AO7</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>N</td>
      <td>N</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>05/2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Schools Officer</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>N</td>
      <td>N</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Resignation-Other reasons</td>
      <td>05/2012</td>
      <td>2005.0</td>
      <td>2006.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>A</td>
      <td>N</td>
      <td>A</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Age Retirement</td>
      <td>05/2012</td>
      <td>1970.0</td>
      <td>1989.0</td>
      <td>Head of Curriculum/Head of Special Education</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>N</td>
      <td>A</td>
      <td>M</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 56 columns</p>
</div>




```python
dete_survey_updated = dete_survey.drop(dete_survey.columns[28:49], axis=1)
tafe_survey_updated = tafe_survey.drop(tafe_survey.columns[17:66], axis=1)
print(dete_survey_updated.columns)
print(tafe_survey_updated.columns)
```

    Index(['ID', 'SeparationType', 'Cease Date', 'DETE Start Date',
           'Role Start Date', 'Position', 'Classification', 'Region',
           'Business Unit', 'Employment Status', 'Career move to public sector',
           'Career move to private sector', 'Interpersonal conflicts',
           'Job dissatisfaction', 'Dissatisfaction with the department',
           'Physical work environment', 'Lack of recognition',
           'Lack of job security', 'Work location', 'Employment conditions',
           'Maternity/family', 'Relocation', 'Study/Travel', 'Ill Health',
           'Traumatic incident', 'Work life balance', 'Workload',
           'None of the above', 'Gender', 'Age', 'Aboriginal', 'Torres Strait',
           'South Sea', 'Disability', 'NESB'],
          dtype='object')
    Index(['Record ID', 'Institute', 'WorkArea', 'CESSATION YEAR',
           'Reason for ceasing employment',
           'Contributing Factors. Career Move - Public Sector ',
           'Contributing Factors. Career Move - Private Sector ',
           'Contributing Factors. Career Move - Self-employment',
           'Contributing Factors. Ill Health',
           'Contributing Factors. Maternity/Family',
           'Contributing Factors. Dissatisfaction',
           'Contributing Factors. Job Dissatisfaction',
           'Contributing Factors. Interpersonal Conflict',
           'Contributing Factors. Study', 'Contributing Factors. Travel',
           'Contributing Factors. Other', 'Contributing Factors. NONE',
           'Gender. What is your Gender?', 'CurrentAge. Current Age',
           'Employment Type. Employment Type', 'Classification. Classification',
           'LengthofServiceOverall. Overall Length of Service at Institute (in years)',
           'LengthofServiceCurrent. Length of Service at current workplace (in years)'],
          dtype='object')
    

The columns that were removed were not necessary to the analysis of exit surveys


```python
dete_survey_updated.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>ID</th>
      <th>SeparationType</th>
      <th>Cease Date</th>
      <th>DETE Start Date</th>
      <th>Role Start Date</th>
      <th>Position</th>
      <th>Classification</th>
      <th>Region</th>
      <th>Business Unit</th>
      <th>Employment Status</th>
      <th>...</th>
      <th>Work life balance</th>
      <th>Workload</th>
      <th>None of the above</th>
      <th>Gender</th>
      <th>Age</th>
      <th>Aboriginal</th>
      <th>Torres Strait</th>
      <th>South Sea</th>
      <th>Disability</th>
      <th>NESB</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Ill Health Retirement</td>
      <td>08/2012</td>
      <td>1984.0</td>
      <td>2004.0</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Yes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>08/2012</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Public Servant</td>
      <td>AO5-AO7</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>05/2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Schools Officer</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Resignation-Other reasons</td>
      <td>05/2012</td>
      <td>2005.0</td>
      <td>2006.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Age Retirement</td>
      <td>05/2012</td>
      <td>1970.0</td>
      <td>1989.0</td>
      <td>Head of Curriculum/Head of Special Education</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 35 columns</p>
</div>




```python
tafe_survey_updated.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Record ID</th>
      <th>Institute</th>
      <th>WorkArea</th>
      <th>CESSATION YEAR</th>
      <th>Reason for ceasing employment</th>
      <th>Contributing Factors. Career Move - Public Sector</th>
      <th>Contributing Factors. Career Move - Private Sector</th>
      <th>Contributing Factors. Career Move - Self-employment</th>
      <th>Contributing Factors. Ill Health</th>
      <th>Contributing Factors. Maternity/Family</th>
      <th>...</th>
      <th>Contributing Factors. Study</th>
      <th>Contributing Factors. Travel</th>
      <th>Contributing Factors. Other</th>
      <th>Contributing Factors. NONE</th>
      <th>Gender. What is your Gender?</th>
      <th>CurrentAge. Current Age</th>
      <th>Employment Type. Employment Type</th>
      <th>Classification. Classification</th>
      <th>LengthofServiceOverall. Overall Length of Service at Institute (in years)</th>
      <th>LengthofServiceCurrent. Length of Service at current workplace (in years)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6.341330e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Contract Expired</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6.341337e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Retirement</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6.341388e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Retirement</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NONE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>6.341399e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6.341466e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>41  45</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 23 columns</p>
</div>



# Clean Column names


```python
dete_survey_updated.columns
```




    Index(['ID', 'SeparationType', 'Cease Date', 'DETE Start Date',
           'Role Start Date', 'Position', 'Classification', 'Region',
           'Business Unit', 'Employment Status', 'Career move to public sector',
           'Career move to private sector', 'Interpersonal conflicts',
           'Job dissatisfaction', 'Dissatisfaction with the department',
           'Physical work environment', 'Lack of recognition',
           'Lack of job security', 'Work location', 'Employment conditions',
           'Maternity/family', 'Relocation', 'Study/Travel', 'Ill Health',
           'Traumatic incident', 'Work life balance', 'Workload',
           'None of the above', 'Gender', 'Age', 'Aboriginal', 'Torres Strait',
           'South Sea', 'Disability', 'NESB'],
          dtype='object')




```python
tafe_survey_updated.columns
```




    Index(['Record ID', 'Institute', 'WorkArea', 'CESSATION YEAR',
           'Reason for ceasing employment',
           'Contributing Factors. Career Move - Public Sector ',
           'Contributing Factors. Career Move - Private Sector ',
           'Contributing Factors. Career Move - Self-employment',
           'Contributing Factors. Ill Health',
           'Contributing Factors. Maternity/Family',
           'Contributing Factors. Dissatisfaction',
           'Contributing Factors. Job Dissatisfaction',
           'Contributing Factors. Interpersonal Conflict',
           'Contributing Factors. Study', 'Contributing Factors. Travel',
           'Contributing Factors. Other', 'Contributing Factors. NONE',
           'Gender. What is your Gender?', 'CurrentAge. Current Age',
           'Employment Type. Employment Type', 'Classification. Classification',
           'LengthofServiceOverall. Overall Length of Service at Institute (in years)',
           'LengthofServiceCurrent. Length of Service at current workplace (in years)'],
          dtype='object')




```python
dete_survey_updated.columns = dete_survey_updated.columns.str.lower().str.strip().str.replace(' ','_')
dete_survey_updated.columns
```




    Index(['id', 'separationtype', 'cease_date', 'dete_start_date',
           'role_start_date', 'position', 'classification', 'region',
           'business_unit', 'employment_status', 'career_move_to_public_sector',
           'career_move_to_private_sector', 'interpersonal_conflicts',
           'job_dissatisfaction', 'dissatisfaction_with_the_department',
           'physical_work_environment', 'lack_of_recognition',
           'lack_of_job_security', 'work_location', 'employment_conditions',
           'maternity/family', 'relocation', 'study/travel', 'ill_health',
           'traumatic_incident', 'work_life_balance', 'workload',
           'none_of_the_above', 'gender', 'age', 'aboriginal', 'torres_strait',
           'south_sea', 'disability', 'nesb'],
          dtype='object')




```python
new_names = {'Record ID': 'id', 'CESSATION YEAR': 'cease_date', 'Reason for ceasing employment': 'separationtype', 'Gender. What is your Gender?': 'gender', 'CurrentAge. Current Age': 'age','Employment Type. Employment Type': 'employment_status','Classification. Classification': 'position', 'LengthofServiceOverall. Overall Length of Service at Institute (in years)': 'institute_service','LengthofServiceCurrent. Length of Service at current workplace (in years)': 'role_service'}
tafe_survey_updated = tafe_survey_updated.rename(new_names,axis=1)
```


```python
dete_survey_updated.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>separationtype</th>
      <th>cease_date</th>
      <th>dete_start_date</th>
      <th>role_start_date</th>
      <th>position</th>
      <th>classification</th>
      <th>region</th>
      <th>business_unit</th>
      <th>employment_status</th>
      <th>...</th>
      <th>work_life_balance</th>
      <th>workload</th>
      <th>none_of_the_above</th>
      <th>gender</th>
      <th>age</th>
      <th>aboriginal</th>
      <th>torres_strait</th>
      <th>south_sea</th>
      <th>disability</th>
      <th>nesb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Ill Health Retirement</td>
      <td>08/2012</td>
      <td>1984.0</td>
      <td>2004.0</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Yes</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>08/2012</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Public Servant</td>
      <td>AO5-AO7</td>
      <td>Central Office</td>
      <td>Corporate Strategy and Peformance</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Voluntary Early Retirement (VER)</td>
      <td>05/2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Schools Officer</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Resignation-Other reasons</td>
      <td>05/2012</td>
      <td>2005.0</td>
      <td>2006.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Age Retirement</td>
      <td>05/2012</td>
      <td>1970.0</td>
      <td>1989.0</td>
      <td>Head of Curriculum/Head of Special Education</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 35 columns</p>
</div>




```python
tafe_survey_updated.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>Institute</th>
      <th>WorkArea</th>
      <th>cease_date</th>
      <th>separationtype</th>
      <th>Contributing Factors. Career Move - Public Sector</th>
      <th>Contributing Factors. Career Move - Private Sector</th>
      <th>Contributing Factors. Career Move - Self-employment</th>
      <th>Contributing Factors. Ill Health</th>
      <th>Contributing Factors. Maternity/Family</th>
      <th>...</th>
      <th>Contributing Factors. Study</th>
      <th>Contributing Factors. Travel</th>
      <th>Contributing Factors. Other</th>
      <th>Contributing Factors. NONE</th>
      <th>gender</th>
      <th>age</th>
      <th>employment_status</th>
      <th>position</th>
      <th>institute_service</th>
      <th>role_service</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6.341330e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Contract Expired</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>1</th>
      <td>6.341337e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Retirement</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2</th>
      <td>6.341388e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Retirement</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NONE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>3</th>
      <td>6.341399e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6.341466e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>41  45</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
  </tbody>
</table>
<p>5 rows × 23 columns</p>
</div>




```python
tafe_survey_updated.columns
```




    Index(['id', 'Institute', 'WorkArea', 'cease_date', 'separationtype',
           'Contributing Factors. Career Move - Public Sector ',
           'Contributing Factors. Career Move - Private Sector ',
           'Contributing Factors. Career Move - Self-employment',
           'Contributing Factors. Ill Health',
           'Contributing Factors. Maternity/Family',
           'Contributing Factors. Dissatisfaction',
           'Contributing Factors. Job Dissatisfaction',
           'Contributing Factors. Interpersonal Conflict',
           'Contributing Factors. Study', 'Contributing Factors. Travel',
           'Contributing Factors. Other', 'Contributing Factors. NONE', 'gender',
           'age', 'employment_status', 'position', 'institute_service',
           'role_service'],
          dtype='object')



# Filtering the data


```python
# separation types for dete survey
dete_survey_updated['separationtype'].value_counts()
```




    Age Retirement                          285
    Resignation-Other reasons               150
    Resignation-Other employer               91
    Resignation-Move overseas/interstate     70
    Voluntary Early Retirement (VER)         67
    Ill Health Retirement                    61
    Other                                    49
    Contract Expired                         34
    Termination                              15
    Name: separationtype, dtype: int64




```python
tafe_survey_updated['separationtype'].value_counts()
```




    Resignation                 340
    Contract Expired            127
    Retrenchment/ Redundancy    104
    Retirement                   82
    Transfer                     25
    Termination                  23
    Name: separationtype, dtype: int64




```python
dete_survey_updated['separationtype'] = dete_survey_updated['separationtype'].str.split('-').str[0]
```


```python
dete_survey_updated['separationtype'].value_counts()
```




    Resignation                         311
    Age Retirement                      285
    Voluntary Early Retirement (VER)     67
    Ill Health Retirement                61
    Other                                49
    Contract Expired                     34
    Termination                          15
    Name: separationtype, dtype: int64




```python
dete_resignations = dete_survey_updated[dete_survey_updated['separationtype'] == 'Resignation'].copy()
```


```python
tafe_resignations = tafe_survey_updated[tafe_survey_updated['separationtype'] == 'Resignation'].copy()
```


```python
dete_resignations
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>separationtype</th>
      <th>cease_date</th>
      <th>dete_start_date</th>
      <th>role_start_date</th>
      <th>position</th>
      <th>classification</th>
      <th>region</th>
      <th>business_unit</th>
      <th>employment_status</th>
      <th>...</th>
      <th>work_life_balance</th>
      <th>workload</th>
      <th>none_of_the_above</th>
      <th>gender</th>
      <th>age</th>
      <th>aboriginal</th>
      <th>torres_strait</th>
      <th>south_sea</th>
      <th>disability</th>
      <th>nesb</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Resignation</td>
      <td>05/2012</td>
      <td>2005.0</td>
      <td>2006.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6</td>
      <td>Resignation</td>
      <td>05/2012</td>
      <td>1994.0</td>
      <td>1997.0</td>
      <td>Guidance Officer</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>8</th>
      <td>9</td>
      <td>Resignation</td>
      <td>07/2012</td>
      <td>2009.0</td>
      <td>2009.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>North Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>31-35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>9</th>
      <td>10</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1997.0</td>
      <td>2008.0</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>11</th>
      <td>12</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2009.0</td>
      <td>2009.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Far North Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>31-35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>12</th>
      <td>13</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1998.0</td>
      <td>1998.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Far North Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>14</th>
      <td>15</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2007.0</td>
      <td>2010.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>31-35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>16</th>
      <td>17</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>20</th>
      <td>21</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1982.0</td>
      <td>1982.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>21</th>
      <td>22</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1980.0</td>
      <td>2009.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>51-55</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>22</th>
      <td>23</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1997.0</td>
      <td>1998.0</td>
      <td>School Administrative Staff</td>
      <td>NaN</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>23</th>
      <td>24</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1973.0</td>
      <td>2012.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>North Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>25</th>
      <td>26</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1995.0</td>
      <td>2002.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>27</th>
      <td>28</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2005.0</td>
      <td>2011.0</td>
      <td>Public Servant</td>
      <td>AO5-AO7</td>
      <td>Central Office</td>
      <td>Information and Technologies</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>21-25</td>
      <td>Yes</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>33</th>
      <td>34</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2003.0</td>
      <td>2003.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Yes</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>34</th>
      <td>35</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2006.0</td>
      <td>2009.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>37</th>
      <td>38</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Temporary Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>39</th>
      <td>40</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Teacher</td>
      <td>NaN</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>40</th>
      <td>41</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1977.0</td>
      <td>1980.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>56-60</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>41</th>
      <td>42</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1974.0</td>
      <td>1994.0</td>
      <td>Head of Curriculum/Head of Special Education</td>
      <td>NaN</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>51-55</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>42</th>
      <td>43</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>North Coast</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>43</th>
      <td>44</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1976.0</td>
      <td>1976.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>North Coast</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
      <td>Male</td>
      <td>51-55</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>48</th>
      <td>49</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2009.0</td>
      <td>2010.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>50</th>
      <td>51</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2009.0</td>
      <td>2010.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>51</th>
      <td>52</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>1993.0</td>
      <td>1993.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>55</th>
      <td>56</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2008.0</td>
      <td>2008.0</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>26-30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>57</th>
      <td>58</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2003.0</td>
      <td>2012.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>61</th>
      <td>62</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Schools Officer</td>
      <td>NaN</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Temporary Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>31-35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>69</th>
      <td>70</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2006.0</td>
      <td>NaN</td>
      <td>Public Servant</td>
      <td>AO5-AO7</td>
      <td>Central Office</td>
      <td>Information and Technologies</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>71</th>
      <td>72</td>
      <td>Resignation</td>
      <td>2012</td>
      <td>2011.0</td>
      <td>2011.0</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Far North Queensland</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>747</th>
      <td>749</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2008.0</td>
      <td>2008.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>North Coast</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>751</th>
      <td>753</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>2005.0</td>
      <td>2005.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>752</th>
      <td>754</td>
      <td>Resignation</td>
      <td>11/2013</td>
      <td>1998.0</td>
      <td>NaN</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Yes</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>753</th>
      <td>755</td>
      <td>Resignation</td>
      <td>11/2013</td>
      <td>2004.0</td>
      <td>2011.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>31-35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>755</th>
      <td>757</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>2012.0</td>
      <td>2013.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>762</th>
      <td>764</td>
      <td>Resignation</td>
      <td>07/2006</td>
      <td>2006.0</td>
      <td>2006.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>766</th>
      <td>768</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2007.0</td>
      <td>2007.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>26-30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>769</th>
      <td>771</td>
      <td>Resignation</td>
      <td>11/2013</td>
      <td>2008.0</td>
      <td>NaN</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>770</th>
      <td>772</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>1987.0</td>
      <td>1987.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>61 or older</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>771</th>
      <td>773</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2002.0</td>
      <td>2007.0</td>
      <td>Head of Curriculum/Head of Special Education</td>
      <td>NaN</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>774</th>
      <td>776</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>2005.0</td>
      <td>2005.0</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>784</th>
      <td>786</td>
      <td>Resignation</td>
      <td>10/2013</td>
      <td>2013.0</td>
      <td>2013.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>786</th>
      <td>788</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>1994.0</td>
      <td>2008.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>788</th>
      <td>790</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>1990.0</td>
      <td>2010.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>789</th>
      <td>791</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>1983.0</td>
      <td>1999.0</td>
      <td>School Based Professional Staff (Therapist, nu...</td>
      <td>NaN</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>Female</td>
      <td>51-55</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>790</th>
      <td>792</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2008.0</td>
      <td>2008.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>North Coast</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>Male</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>791</th>
      <td>793</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>2007.0</td>
      <td>2007.0</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>794</th>
      <td>796</td>
      <td>Resignation</td>
      <td>11/2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>797</th>
      <td>799</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>2000.0</td>
      <td>2013.0</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>South East</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>798</th>
      <td>800</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>1995.0</td>
      <td>NaN</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>802</th>
      <td>804</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>803</th>
      <td>805</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2004.0</td>
      <td>2007.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>46-50</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>804</th>
      <td>806</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2008.0</td>
      <td>2013.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Darling Downs South West</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>26-30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>806</th>
      <td>808</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>2005.0</td>
      <td>2005.0</td>
      <td>Cleaner</td>
      <td>NaN</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>41-45</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>807</th>
      <td>809</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>2004.0</td>
      <td>2004.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Central Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>36-40</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>808</th>
      <td>810</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>2010.0</td>
      <td>2010.0</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Permanent Part-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>26-30</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>815</th>
      <td>817</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2012.0</td>
      <td>2012.0</td>
      <td>Teacher</td>
      <td>Primary</td>
      <td>Far North Queensland</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Male</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>816</th>
      <td>818</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2012.0</td>
      <td>2012.0</td>
      <td>Teacher</td>
      <td>Secondary</td>
      <td>North Coast</td>
      <td>NaN</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>21-25</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>819</th>
      <td>821</td>
      <td>Resignation</td>
      <td>01/2014</td>
      <td>2009.0</td>
      <td>2009.0</td>
      <td>Public Servant</td>
      <td>A01-A04</td>
      <td>Central Office</td>
      <td>Education Queensland</td>
      <td>Permanent Full-time</td>
      <td>...</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>Female</td>
      <td>31-35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>821</th>
      <td>823</td>
      <td>Resignation</td>
      <td>12/2013</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Teacher Aide</td>
      <td>NaN</td>
      <td>Metropolitan</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>311 rows × 35 columns</p>
</div>




```python
tafe_resignations
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>id</th>
      <th>Institute</th>
      <th>WorkArea</th>
      <th>cease_date</th>
      <th>separationtype</th>
      <th>Contributing Factors. Career Move - Public Sector</th>
      <th>Contributing Factors. Career Move - Private Sector</th>
      <th>Contributing Factors. Career Move - Self-employment</th>
      <th>Contributing Factors. Ill Health</th>
      <th>Contributing Factors. Maternity/Family</th>
      <th>...</th>
      <th>Contributing Factors. Study</th>
      <th>Contributing Factors. Travel</th>
      <th>Contributing Factors. Other</th>
      <th>Contributing Factors. NONE</th>
      <th>gender</th>
      <th>age</th>
      <th>employment_status</th>
      <th>position</th>
      <th>institute_service</th>
      <th>role_service</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>3</th>
      <td>6.341399e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>4</th>
      <td>6.341466e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>41  45</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>5</th>
      <td>6.341475e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>56 or older</td>
      <td>Contract/casual</td>
      <td>Teacher (including LVT)</td>
      <td>7-10</td>
      <td>7-10</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6.341520e+17</td>
      <td>Barrier Reef Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>Maternity/Family</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Male</td>
      <td>20 or younger</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>7</th>
      <td>6.341537e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Male</td>
      <td>46  50</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>8</th>
      <td>6.341579e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2009.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>36  40</td>
      <td>Temporary Full-time</td>
      <td>Tutor</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>9</th>
      <td>6.341588e+17</td>
      <td>Barrier Reef Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>21  25</td>
      <td>Permanent Full-time</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>10</th>
      <td>6.341588e+17</td>
      <td>Barrier Reef Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>41  45</td>
      <td>Temporary Part-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>13</th>
      <td>6.341725e+17</td>
      <td>Barrier Reef Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>31  35</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>11-20</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>14</th>
      <td>6.341726e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>31  35</td>
      <td>Permanent Part-time</td>
      <td>Teacher (including LVT)</td>
      <td>7-10</td>
      <td>7-10</td>
    </tr>
    <tr>
      <th>15</th>
      <td>6.341761e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>46  50</td>
      <td>Permanent Part-time</td>
      <td>Technical Officer (TO)</td>
      <td>11-20</td>
      <td>11-20</td>
    </tr>
    <tr>
      <th>16</th>
      <td>6.341770e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>17</th>
      <td>6.341771e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>31  35</td>
      <td>Permanent Full-time</td>
      <td>Administration (AO)</td>
      <td>7-10</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>18</th>
      <td>6.341779e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>19</th>
      <td>6.341820e+17</td>
      <td>Southbank Institute of Technology</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NONE</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>20</th>
      <td>6.341821e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>21</th>
      <td>6.341831e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>22</th>
      <td>6.341847e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Male</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>5-6</td>
      <td>5-6</td>
    </tr>
    <tr>
      <th>23</th>
      <td>6.341907e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Contract/casual</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>24</th>
      <td>6.341907e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>31  35</td>
      <td>Permanent Part-time</td>
      <td>Teacher (including LVT)</td>
      <td>7-10</td>
      <td>7-10</td>
    </tr>
    <tr>
      <th>26</th>
      <td>6.341934e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>27</th>
      <td>6.341935e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>56 or older</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>More than 20 years</td>
      <td>7-10</td>
    </tr>
    <tr>
      <th>29</th>
      <td>6.341994e+17</td>
      <td>Barrier Reef Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>31  35</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>32</th>
      <td>6.342002e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>46  50</td>
      <td>Temporary Part-time</td>
      <td>Professional Officer (PO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>36</th>
      <td>6.342062e+17</td>
      <td>Sunshine Coast Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>37</th>
      <td>6.342080e+17</td>
      <td>Southbank Institute of Technology</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>39</th>
      <td>6.342081e+17</td>
      <td>Southbank Institute of Technology</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>40</th>
      <td>6.342090e+17</td>
      <td>Sunshine Coast Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Part-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>41</th>
      <td>6.342090e+17</td>
      <td>Sunshine Coast Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>Career Move - Self-employment</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>36  40</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>11-20</td>
      <td>11-20</td>
    </tr>
    <tr>
      <th>42</th>
      <td>6.342090e+17</td>
      <td>Sunshine Coast Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>Career Move - Self-employment</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>31  35</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>659</th>
      <td>6.349985e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Study</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>46  50</td>
      <td>Temporary Part-time</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>660</th>
      <td>6.349994e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>41  45</td>
      <td>Permanent Part-time</td>
      <td>Administration (AO)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>661</th>
      <td>6.350003e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>46  50</td>
      <td>Permanent Full-time</td>
      <td>Administration (AO)</td>
      <td>5-6</td>
      <td>More than 20 years</td>
    </tr>
    <tr>
      <th>665</th>
      <td>6.350055e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>666</th>
      <td>6.350055e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>669</th>
      <td>6.350108e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>670</th>
      <td>6.350124e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>671</th>
      <td>6.350127e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>46  50</td>
      <td>Temporary Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>675</th>
      <td>6.350175e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Male</td>
      <td>51-55</td>
      <td>Temporary Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>676</th>
      <td>6.350194e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>41  45</td>
      <td>Contract/casual</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>677</th>
      <td>6.350219e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>36  40</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>678</th>
      <td>6.350253e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>51-55</td>
      <td>Permanent Full-time</td>
      <td>Administration (AO)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>679</th>
      <td>6.350279e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>56 or older</td>
      <td>Temporary Part-time</td>
      <td>Operational (OO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>681</th>
      <td>6.350314e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>682</th>
      <td>6.350357e+17</td>
      <td>Mount Isa Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Permanent Part-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>683</th>
      <td>6.350374e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>41  45</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>684</th>
      <td>6.350375e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>41  45</td>
      <td>Contract/casual</td>
      <td>Administration (AO)</td>
      <td>3-4</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>685</th>
      <td>6.350402e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Technical Officer (TO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>686</th>
      <td>6.350426e+17</td>
      <td>Brisbane North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>41  45</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>5-6</td>
      <td>5-6</td>
    </tr>
    <tr>
      <th>688</th>
      <td>6.350479e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NONE</td>
      <td>Female</td>
      <td>46  50</td>
      <td>Permanent Part-time</td>
      <td>Professional Officer (PO)</td>
      <td>5-6</td>
      <td>5-6</td>
    </tr>
    <tr>
      <th>689</th>
      <td>6.350480e+17</td>
      <td>Central Queensland Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>41  45</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>Less than 1 year</td>
      <td>Less than 1 year</td>
    </tr>
    <tr>
      <th>690</th>
      <td>6.350496e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>691</th>
      <td>6.350496e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>56 or older</td>
      <td>Permanent Part-time</td>
      <td>Operational (OO)</td>
      <td>3-4</td>
      <td>3-4</td>
    </tr>
    <tr>
      <th>693</th>
      <td>6.350599e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Temporary Full-time</td>
      <td>Administration (AO)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>694</th>
      <td>6.350652e+17</td>
      <td>Sunshine Coast Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>696</th>
      <td>6.350660e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>21  25</td>
      <td>Temporary Full-time</td>
      <td>Operational (OO)</td>
      <td>5-6</td>
      <td>5-6</td>
    </tr>
    <tr>
      <th>697</th>
      <td>6.350668e+17</td>
      <td>Barrier Reef Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Male</td>
      <td>51-55</td>
      <td>Temporary Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>1-2</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>698</th>
      <td>6.350677e+17</td>
      <td>Southern Queensland Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>699</th>
      <td>6.350704e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Delivery (teaching)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>-</td>
      <td>Female</td>
      <td>51-55</td>
      <td>Permanent Full-time</td>
      <td>Teacher (including LVT)</td>
      <td>5-6</td>
      <td>1-2</td>
    </tr>
    <tr>
      <th>701</th>
      <td>6.350730e+17</td>
      <td>Tropical North Institute of TAFE</td>
      <td>Non-Delivery (corporate)</td>
      <td>2013.0</td>
      <td>Resignation</td>
      <td>-</td>
      <td>-</td>
      <td>Career Move - Self-employment</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>-</td>
      <td>Travel</td>
      <td>-</td>
      <td>-</td>
      <td>Female</td>
      <td>26  30</td>
      <td>Contract/casual</td>
      <td>Administration (AO)</td>
      <td>3-4</td>
      <td>1-2</td>
    </tr>
  </tbody>
</table>
<p>340 rows × 23 columns</p>
</div>



# Verify the Data


```python
dete_resignations['cease_date'].value_counts()
```




    2012       126
    2013        74
    01/2014     22
    12/2013     17
    06/2013     14
    09/2013     11
    11/2013      9
    07/2013      9
    10/2013      6
    08/2013      4
    05/2012      2
    05/2013      2
    2010         1
    07/2012      1
    09/2010      1
    07/2006      1
    Name: cease_date, dtype: int64




```python
dete_resignations['cease_date'] = dete_resignations['cease_date'].str.split('/').str[-1]
dete_resignations['cease_date'] = dete_resignations['cease_date'].astype('float')

dete_resignations['cease_date'].value_counts() # verify years
```




    2013.0    146
    2012.0    129
    2014.0     22
    2010.0      2
    2006.0      1
    Name: cease_date, dtype: int64




```python
dete_resignations['dete_start_date'].value_counts().sort_index()
```




    1963.0     1
    1971.0     1
    1972.0     1
    1973.0     1
    1974.0     2
    1975.0     1
    1976.0     2
    1977.0     1
    1980.0     5
    1982.0     1
    1983.0     2
    1984.0     1
    1985.0     3
    1986.0     3
    1987.0     1
    1988.0     4
    1989.0     4
    1990.0     5
    1991.0     4
    1992.0     6
    1993.0     5
    1994.0     6
    1995.0     4
    1996.0     6
    1997.0     5
    1998.0     6
    1999.0     8
    2000.0     9
    2001.0     3
    2002.0     6
    2003.0     6
    2004.0    14
    2005.0    15
    2006.0    13
    2007.0    21
    2008.0    22
    2009.0    13
    2010.0    17
    2011.0    24
    2012.0    21
    2013.0    10
    Name: dete_start_date, dtype: int64




```python
tafe_resignations['cease_date'].value_counts()
```




    2011.0    116
    2012.0     94
    2010.0     68
    2013.0     55
    2009.0      2
    Name: cease_date, dtype: int64



More resignations in tafe in 2011 however, the years don't line up.  We are not analyzing by year so we can leave it be

# Create a new column


```python
dete_resignations['institute_service'] = dete_resignations['cease_date'] - dete_resignations['dete_start_date']
```


```python
dete_resignations['institute_service'].value_counts()
```




    5.0     23
    1.0     22
    3.0     20
    0.0     20
    6.0     17
    4.0     16
    9.0     14
    2.0     14
    7.0     13
    13.0     8
    8.0      8
    20.0     7
    15.0     7
    10.0     6
    22.0     6
    14.0     6
    17.0     6
    12.0     6
    16.0     5
    18.0     5
    23.0     4
    11.0     4
    24.0     4
    39.0     3
    19.0     3
    21.0     3
    32.0     3
    28.0     2
    26.0     2
    25.0     2
    30.0     2
    36.0     2
    29.0     1
    33.0     1
    42.0     1
    27.0     1
    41.0     1
    35.0     1
    38.0     1
    34.0     1
    49.0     1
    31.0     1
    Name: institute_service, dtype: int64



By identifying how long an employee is working for we can answer the question in the end goal


```python
dete_resignations['institute_service'].head()
```




    3      7.0
    5     18.0
    8      3.0
    9     15.0
    11     3.0
    Name: institute_service, dtype: float64



# Identify Dissatified Employees


```python
tafe_resignations['Contributing Factors. Dissatisfaction'].value_counts()
```




    -                                         277
    Contributing Factors. Dissatisfaction      55
    Name: Contributing Factors. Dissatisfaction, dtype: int64




```python
tafe_resignations['Contributing Factors. Job Dissatisfaction'].value_counts()
```




    -                      270
    Job Dissatisfaction     62
    Name: Contributing Factors. Job Dissatisfaction, dtype: int64




```python
def update_vals(element):
    if element == '-':
        return False
    elif pd.isnull(element):
        return np.nan
    else:
        return True

tafe_resignations['dissatisfied'] = tafe_resignations[['Contributing Factors. Dissatisfaction','Contributing Factors. Job Dissatisfaction']].applymap(update_vals).any(axis=1, skipna=False)

# Make copy
tafe_resignations_up = tafe_resignations.copy()

# Check values
tafe_resignations_up['dissatisfied'].value_counts(dropna=False)
```




    False    241
    True      91
    NaN        8
    Name: dissatisfied, dtype: int64




```python
dete_resignations['dissatisfied'] = dete_resignations[['job_dissatisfaction','dissatisfaction_with_the_department','physical_work_environment','lack_of_recognition','lack_of_job_security','work_location','employment_conditions','work_life_balance','workload']].any(1, skipna=False)

# Make copy
dete_resignations_up = dete_resignations.copy()

# Check values
dete_resignations_up['dissatisfied'].value_counts(dropna=False)
```




    False    162
    True     149
    Name: dissatisfied, dtype: int64



# Combine the Data


```python
# Create new columns
dete_resignations_up['institute'] = 'DETE'
tafe_resignations_up['institute'] = 'TAFE'
```


```python
# Combine the dataframes 
combined = pd.concat([dete_resignations_up,tafe_resignations_up],ignore_index=True)
```


```python
# look at the new combined dataframe
combined
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Contributing Factors. Career Move - Private Sector</th>
      <th>Contributing Factors. Career Move - Public Sector</th>
      <th>Contributing Factors. Career Move - Self-employment</th>
      <th>Contributing Factors. Dissatisfaction</th>
      <th>Contributing Factors. Ill Health</th>
      <th>Contributing Factors. Interpersonal Conflict</th>
      <th>Contributing Factors. Job Dissatisfaction</th>
      <th>Contributing Factors. Maternity/Family</th>
      <th>Contributing Factors. NONE</th>
      <th>Contributing Factors. Other</th>
      <th>...</th>
      <th>role_service</th>
      <th>role_start_date</th>
      <th>separationtype</th>
      <th>south_sea</th>
      <th>study/travel</th>
      <th>torres_strait</th>
      <th>traumatic_incident</th>
      <th>work_life_balance</th>
      <th>work_location</th>
      <th>workload</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2006.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1997.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2009.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2008.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2009.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1998.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>6</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>7</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>8</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1982.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>9</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2009.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>10</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1998.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>11</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2012.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>12</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2002.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>13</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2011.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>14</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2003.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>15</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2009.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>16</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2011.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>17</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>18</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1980.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>19</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1994.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>20</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2011.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>21</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1976.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
      <td>True</td>
    </tr>
    <tr>
      <th>22</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>23</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2010.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>24</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>1993.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>25</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2008.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>26</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2012.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>27</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2011.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>28</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>True</td>
      <td>False</td>
    </tr>
    <tr>
      <th>29</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>2011.0</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>False</td>
      <td>NaN</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
      <td>False</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>621</th>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>622</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>3-4</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>623</th>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Job Dissatisfaction</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>More than 20 years</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>624</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>625</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>626</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>3-4</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>627</th>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>628</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Job Dissatisfaction</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>629</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Contributing Factors. Dissatisfaction</td>
      <td>-</td>
      <td>Interpersonal Conflict</td>
      <td>Job Dissatisfaction</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>630</th>
      <td>-</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>631</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>632</th>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>3-4</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>633</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>634</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>635</th>
      <td>-</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>636</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>637</th>
      <td>-</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>638</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Contributing Factors. Dissatisfaction</td>
      <td>-</td>
      <td>-</td>
      <td>Job Dissatisfaction</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>639</th>
      <td>-</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>5-6</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>640</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>NONE</td>
      <td>-</td>
      <td>...</td>
      <td>5-6</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>641</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>Job Dissatisfaction</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>Less than 1 year</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>642</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>643</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Ill Health</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>3-4</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>644</th>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>645</th>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>646</th>
      <td>Career Move - Private Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>5-6</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>647</th>
      <td>-</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>648</th>
      <td>-</td>
      <td>Career Move - Public Sector</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>649</th>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>Other</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
    <tr>
      <th>650</th>
      <td>-</td>
      <td>-</td>
      <td>Career Move - Self-employment</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>-</td>
      <td>...</td>
      <td>1-2</td>
      <td>NaN</td>
      <td>Resignation</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
    </tr>
  </tbody>
</table>
<p>651 rows × 53 columns</p>
</div>




```python
# Verify non NaN values
combined.notnull().sum().sort_values()
```




    torres_strait                                            0
    south_sea                                                3
    aboriginal                                               7
    disability                                               8
    nesb                                                     9
    business_unit                                           32
    classification                                         161
    region                                                 265
    role_start_date                                        271
    dete_start_date                                        283
    role_service                                           290
    career_move_to_public_sector                           311
    employment_conditions                                  311
    work_location                                          311
    lack_of_job_security                                   311
    job_dissatisfaction                                    311
    dissatisfaction_with_the_department                    311
    workload                                               311
    lack_of_recognition                                    311
    interpersonal_conflicts                                311
    maternity/family                                       311
    none_of_the_above                                      311
    physical_work_environment                              311
    relocation                                             311
    study/travel                                           311
    traumatic_incident                                     311
    work_life_balance                                      311
    career_move_to_private_sector                          311
    ill_health                                             311
    Contributing Factors. Career Move - Private Sector     332
    Contributing Factors. Other                            332
    Contributing Factors. Career Move - Public Sector      332
    Contributing Factors. Career Move - Self-employment    332
    Contributing Factors. Travel                           332
    Contributing Factors. Study                            332
    Contributing Factors. Dissatisfaction                  332
    Contributing Factors. Ill Health                       332
    Contributing Factors. NONE                             332
    Contributing Factors. Maternity/Family                 332
    Contributing Factors. Job Dissatisfaction              332
    Contributing Factors. Interpersonal Conflict           332
    WorkArea                                               340
    Institute                                              340
    institute_service                                      563
    gender                                                 592
    age                                                    596
    employment_status                                      597
    position                                               598
    cease_date                                             635
    dissatisfied                                           643
    id                                                     651
    separationtype                                         651
    institute                                              651
    dtype: int64




```python
# Drop the null values and assign it back to combined_updated with less then 500 non null values
combined_updated = combined.dropna(thresh=500,axis=1).copy()
```


```python
# look at the updated combined dataframe (first 5 rows)
combined_updated.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>age</th>
      <th>cease_date</th>
      <th>dissatisfied</th>
      <th>employment_status</th>
      <th>gender</th>
      <th>id</th>
      <th>institute</th>
      <th>institute_service</th>
      <th>position</th>
      <th>separationtype</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>36-40</td>
      <td>2012.0</td>
      <td>False</td>
      <td>Permanent Full-time</td>
      <td>Female</td>
      <td>4.0</td>
      <td>DETE</td>
      <td>7</td>
      <td>Teacher</td>
      <td>Resignation</td>
    </tr>
    <tr>
      <th>1</th>
      <td>41-45</td>
      <td>2012.0</td>
      <td>True</td>
      <td>Permanent Full-time</td>
      <td>Female</td>
      <td>6.0</td>
      <td>DETE</td>
      <td>18</td>
      <td>Guidance Officer</td>
      <td>Resignation</td>
    </tr>
    <tr>
      <th>2</th>
      <td>31-35</td>
      <td>2012.0</td>
      <td>False</td>
      <td>Permanent Full-time</td>
      <td>Female</td>
      <td>9.0</td>
      <td>DETE</td>
      <td>3</td>
      <td>Teacher</td>
      <td>Resignation</td>
    </tr>
    <tr>
      <th>3</th>
      <td>46-50</td>
      <td>2012.0</td>
      <td>True</td>
      <td>Permanent Part-time</td>
      <td>Female</td>
      <td>10.0</td>
      <td>DETE</td>
      <td>15</td>
      <td>Teacher Aide</td>
      <td>Resignation</td>
    </tr>
    <tr>
      <th>4</th>
      <td>31-35</td>
      <td>2012.0</td>
      <td>False</td>
      <td>Permanent Full-time</td>
      <td>Male</td>
      <td>12.0</td>
      <td>DETE</td>
      <td>3</td>
      <td>Teacher</td>
      <td>Resignation</td>
    </tr>
  </tbody>
</table>
</div>



By combining the data we can see an overall picture of whether employees who were dissatisfied were short term or longer term employees

# Clean the service column


```python
# Get the values of the institute service column
combined_updated['institute_service'].value_counts(dropna=False)
```




    NaN                   88
    Less than 1 year      73
    1-2                   64
    3-4                   63
    5-6                   33
    11-20                 26
    5.0                   23
    1.0                   22
    7-10                  21
    0.0                   20
    3.0                   20
    6.0                   17
    4.0                   16
    9.0                   14
    2.0                   14
    7.0                   13
    More than 20 years    10
    13.0                   8
    8.0                    8
    15.0                   7
    20.0                   7
    10.0                   6
    12.0                   6
    14.0                   6
    17.0                   6
    22.0                   6
    16.0                   5
    18.0                   5
    11.0                   4
    23.0                   4
    24.0                   4
    19.0                   3
    32.0                   3
    39.0                   3
    21.0                   3
    28.0                   2
    30.0                   2
    26.0                   2
    36.0                   2
    25.0                   2
    29.0                   1
    31.0                   1
    27.0                   1
    34.0                   1
    35.0                   1
    38.0                   1
    41.0                   1
    42.0                   1
    49.0                   1
    33.0                   1
    Name: institute_service, dtype: int64




```python
# Extract the years of service from each value from the institute_service column
combined_updated['institute_service_updated'] = combined_updated['institute_service'].astype('str').str.extract(r'(\d+)')
```

    /dataquest/system/env/python3/lib/python3.4/site-packages/ipykernel/__main__.py:2: FutureWarning: currently extract(expand=None) means expand=False (return Index/Series/DataFrame) but in a future version of pandas this will be changed to expand=True (return DataFrame)
      from ipykernel import kernelapp as app
    


```python
combined_updated['institute_service_updated'].value_counts()
```




    1     159
    3      83
    5      56
    7      34
    11     30
    0      20
    20     17
    6      17
    4      16
    2      14
    9      14
    13      8
    8       8
    15      7
    22      6
    12      6
    14      6
    10      6
    17      6
    18      5
    16      5
    23      4
    24      4
    21      3
    39      3
    19      3
    32      3
    36      2
    30      2
    25      2
    28      2
    26      2
    27      1
    33      1
    49      1
    34      1
    31      1
    42      1
    29      1
    35      1
    38      1
    41      1
    Name: institute_service_updated, dtype: int64




```python
# Convert to float
combined_updated['institute_service_updated'] = combined_updated['institute_service_updated'].astype('float')
combined_updated['institute_service_updated'].value_counts()
```




    1.0     159
    3.0      83
    5.0      56
    7.0      34
    11.0     30
    0.0      20
    20.0     17
    6.0      17
    4.0      16
    9.0      14
    2.0      14
    13.0      8
    8.0       8
    15.0      7
    17.0      6
    10.0      6
    12.0      6
    14.0      6
    22.0      6
    16.0      5
    18.0      5
    24.0      4
    23.0      4
    39.0      3
    19.0      3
    21.0      3
    32.0      3
    28.0      2
    36.0      2
    25.0      2
    30.0      2
    26.0      2
    29.0      1
    38.0      1
    42.0      1
    27.0      1
    41.0      1
    35.0      1
    49.0      1
    34.0      1
    33.0      1
    31.0      1
    Name: institute_service_updated, dtype: int64




```python
def convert_year(year):
    if year >= 11:
        return 'Veteran'
    elif 7 <= year < 11:
        return 'Experienced'
    elif 3 <= year < 7:
        return 'Established'
    elif pd.isnull(year):
        return np.nan
    else:
        return 'New'

combined_updated['service_cat'] = combined_updated['institute_service_updated'].apply(convert_year)

# Validate the update
combined_updated['service_cat'].value_counts()
        
```




    New            193
    Established    172
    Veteran        136
    Experienced     62
    Name: service_cat, dtype: int64



We did this to make the analysis easier

# Perform Initial Analysis


```python
combined_updated['service_cat'].value_counts()
```




    New            193
    Established    172
    Veteran        136
    Experienced     62
    Name: service_cat, dtype: int64




```python
# get the number of true and false in the dissatisfied column
combined_updated['dissatisfied'].value_counts(dropna=False)
```




    False    403
    True     240
    NaN        8
    Name: dissatisfied, dtype: int64




```python
# Fill the missing values
combined_updated['dissatisfied'] = combined_updated['dissatisfied'].fillna(False)
```


```python
combined_updated['dissatisfied'].value_counts()
```




    False    411
    True     240
    Name: dissatisfied, dtype: int64




```python
cb_pv_table = combined_updated.pivot_table(index='service_cat',values='dissatisfied')
```


```python
# plot the results
%matplotlib inline
cb_pv_table.plot(kind='bar',rot=30)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x7fdffd284e48>




![png](/images/employee-exit//Basics_66_1.png)


Employees with 7 or more years of experience are more likely to resign due to dissatisfaction however, we need to look at the rest of the data before making any conclusions


```python

```
