# cleisonzw.github.ioo
A free Netflix-inspired web app showcasing movies and TV series with search, favorites, and dark theme.
Now let me update the Detail page:
Action: file_editor str_replace /app/frontend/src/pages/Detail.jsx --old-str "import React, { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import Navbar from '../components/Navbar';
import { Button } from '../components/ui/button';
import { Play, Plus, Check, Star, ArrowLeft } from 'lucide-react';
import { allContent } from '../mockData';
import TrailerModal from '../components/TrailerModal';

const Detail = () => {
  const { id } = useParams();
  const navigate = useNavigate();
  const [content, setContent] = useState(null);
  const [isFavorite, setIsFavorite] = useState(false);
  const [showTrailer, setShowTrailer] = useState(false);

  useEffect(() => {
    const item = allContent.find(c => c.id === parseInt(id));
    setContent(item);
    
    // Check if in favorites (mock)
    const favorites = JSON.parse(localStorage.getItem('favorites') || '[]');
    setIsFavorite(favorites.includes(parseInt(id)));
  }, [id]);

  const toggleFavorite = () => {
    const favorites = JSON.parse(localStorage.getItem('favorites') || '[]');
    if (isFavorite) {
      const updated = favorites.filter(fav => fav !== parseInt(id));
      localStorage.setItem('favorites', JSON.stringify(updated));
      setIsFavorite(false);
    } else {
      favorites.push(parseInt(id));
      localStorage.setItem('favorites', JSON.stringify(favorites));
      setIsFavorite(true);
    }
  };

  if (!content) return <div className=\"min-h-screen bg-black\"></div>;" --new-str "import React, { useState, useEffect } from 'react';
import { useParams, useNavigate } from 'react-router-dom';
import Navbar from '../components/Navbar';
import { Button } from '../components/ui/button';
import { Play, Plus, Check, Star, ArrowLeft } from 'lucide-react';
import { content as contentAPI, user as userAPI } from '../services/api';
import TrailerModal from '../components/TrailerModal';

const Detail = () => {
  const { id } = useParams();
  const navigate = useNavigate();
  const [content, setContent] = useState(null);
  const [isFavorite, setIsFavorite] = useState(false);
  const [showTrailer, setShowTrailer] = useState(false);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    loadContent();
    checkFavorite();
  }, [id]);

  const loadContent = async () => {
    try {
      setLoading(true);
      // Try to get from localStorage first to determine type
      const favorites = await userAPI.getFavorites().catch(() => []);
      const fav = favorites.find(f => f.content_id === parseInt(id));
      const type = fav?.type || 'movie'; // Default to movie if not found
      
      const data = await contentAPI.getDetail(parseInt(id), type);
      setContent(data);
    } catch (error) {
      console.error('Error loading content:', error);
      // If failed, try both types
      try {
        const movieData = await contentAPI.getDetail(parseInt(id), 'movie');
        setContent(movieData);
      } catch {
        try {
          const tvData = await contentAPI.getDetail(parseInt(id), 'tv');
          setContent(tvData);
        } catch (err) {
          console.error('Failed to load content');
        }
      }
    } finally {
      setLoading(false);
    }
  };

  const checkFavorite = async () => {
    try {
      const favorites = await userAPI.getFavorites();
      setIsFavorite(favorites.some(fav => fav.content_id === parseInt(id)));
    } catch (error) {
      console.error('Error checking favorites:', error);
    }
  };

  const toggleFavorite = async () => {
    try {
      if (isFavorite) {
        await userAPI.removeFavorite(parseInt(id));
        setIsFavorite(false);
      } else {
        await userAPI.addFavorite(
          parseInt(id),
          content.type,
          content.title,
          content.poster
        );
        setIsFavorite(true);
      }
    } catch (error) {
      console.error('Error toggling favorite:', error);
    }
  };

  if (loading) {
    return (
      <div className=\"min-h-screen bg-black flex items-center justify-center\">
        <div className=\"text-white text-xl\">Loading...</div>
      </div>
    );
  }

  if (!content) {
    return (
      <div className=\"min-h-screen bg-black\">
        <Navbar />
        <div className=\"flex items-center justify-center min-h-screen\">
          <div className=\"text-white text-xl\">Content not found</div>
        </div>
      </div>
    );
  }"
Observation: Edit was successful.
